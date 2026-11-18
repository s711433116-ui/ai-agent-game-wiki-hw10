# RAG（檢索增強生成）

## 1. Overview
Retrieval-Augmented Generation（RAG，檢索增強生成）是由 Patrick Lewis 等人於 2020 年（NeurIPS 2020）提出的通用微調架構（general-purpose fine-tuning recipe），專為解決知識密集型自然語言處理任務（Knowledge-Intensive NLP Tasks）而設計（Lewis et al., 2020）。

傳統預訓練語言模型（如 T5、BART）雖然能將事實知識隱式儲存於模型參數中，但存在無法精確操作知識、更新世界知識成本高昂、難以提供可檢驗的預測依據（provenance）以及容易產生事實幻覺（hallucinations）等瓶頸。RAG 透過將**參數化記憶（Parametric Memory）**與可微分存取的**非參數化記憶（Non-parametric Memory）**結合為端到端的機率生成模型，使語言模型在生成文本時能夠動態檢索外部知識文件（Lewis et al., 2020）。

## 2. Core Concept
### (1) 參數化記憶 vs. 非參數化記憶（Parametric vs. Non-parametric Memory）
Lewis et al.（2020）將語言模型的知識來源明確拆解為兩個互補組件：
- **Parametric Memory（參數化記憶）**：
  - 形式：預訓練的 Sequence-to-Sequence（seq2seq）神經網路權重。
  - 論文實作：採用具備 400M 參數量的 BART-large 模型。
  - 特性：Parametric Memory 將知識隱式儲存在模型權重中；相較於可直接替換的 Non-parametric Memory，若要更新其中知識通常需要進一步訓練或調整模型參數。
- **Non-parametric Memory（非參數化記憶）**：
  - 形式：外部文本資料庫的稠密向量索引（Dense Vector Index）。
  - 論文實作：使用 2018 年 12 月的 Wikipedia Dump，拆分為 2100 萬個互不重疊的 100 詞文本區塊（100-word chunks），透過 DPR 的預訓練文件編碼器轉化為稠密向量，並建立 FAISS MIPS 索引（結合 HNSW 近似加速）。論文附錄 E 記載該索引包含約 2100 萬個 728 維文件向量（標註為 arXiv / 完整版 Appendix E 的實作資訊）。
  - 特性：具備可讀性（Human-readable）與可寫性（Human-writable）。知識可直接透過修改或替換文件索引進行更新（無需重新訓練神經網路）。

### (2) 兩種 RAG 機率模型形式（RAG Formulations）
RAG 將檢索到的文本 $z$ 視為隱變數（Latent Variable），並在給定輸入 $x$ 生成目標序列 $y$ 時對其進行邊緣化（Marginalization），論文提出了兩種模型變形：
1. **RAG-Sequence Model（全序列單一文件邊緣化）**：
   - 假設同一份檢索文件負責引導生成**整段完整輸出序列**。
   - 透過 Top-$K$ 近似計算整體機率：
     $$p_{\text{RAG-Sequence}}(y \mid x) \approx \sum_{z \in \text{top-}k(p(\cdot \mid x))} p_\eta(z \mid x) \prod_{i=1}^N p_\theta(y_i \mid x, z, y_{1:i-1})$$
2. **RAG-Token Model（每 token 獨立文件邊緣化）**：
   - 允許生成**每個個別 token** 時繪製不同的潛在文件，使模型能夠融合多篇文件的內容來拼湊答案。
   - 機率定義為：
     $$p_{\text{RAG-Token}}(y \mid x) \approx \prod_{i=1}^N \sum_{z \in \text{top-}k(p(\cdot \mid x))} p_\eta(z \mid x) p_\theta(y_i \mid x, z, y_{1:i-1})$$
   - *註：若任務為序列分類（Sequence Classification），目標序列長度為 1，此時 RAG-Sequence 與 RAG-Token 在數學上完全等價。*

> [!NOTE]
> **Wiki 比較性整理：原始 RAG 與現代產業界 RAG 的區別**
> 本頁所述之 RAG 嚴格以 Lewis et al.（2020）原始論文為準，屬於**端到端可微分訓練的 Seq2Seq 聯合微調模型**（DPR 檢索器 + BART 生成器）；現代產業界常見的「向量資料庫 + Chunking 切分 + Embedding API 檢索 + 靜態 Prompt 拼裝交給黑盒 LLM」屬於後續發展的提示工程架構，並非 2020 年原始論文的架構定義。

## 3. Why It Matters
- **改善事實性與減少幻覺**：Lewis et al. (2020) 觀察到 RAG 相較 BART 較少產生 hallucination，且在其生成實驗與人類評估中更常產生 factual、specific 的內容。
- **知識熱插拔（Index Hot-swapping）**：當世界知識隨時間演進改變時，純參數化模型必須耗費龐大運算重新微調；而 RAG 只需直接替換非參數化的文件索引庫，即可更新模型的實體世界知識（Lewis et al., 2020）。
- **可檢驗性與 Provenance**：檢索到的外部文本可被使用者檢視，為生成結果提供可檢查的知識依據與較佳的 provenance；但這不代表每一個生成敘述都必然由特定檢索文件完整支持（Lewis et al., 2020）。
- **參數利用效率**：論文附錄將 RAG 的神經網路參數總量計為約 626M（BART-large 406M，加上 DPR query/document encoders 各約 110M）；但實際 RAG fine-tuning 時固定 document encoder 與 index，只更新 query encoder 與 BART generator。即便實際訓練參數少於 626M，RAG-Sequence 在多項開放領域問答的準確率上，仍能超越參數量高達 110 億（11B）的純參數化模型 T5-11B（Lewis et al., 2020）。

## 4. How It Works
### (1) 模型組成與介面
- **Retriever（檢索器）$p_\eta(z \mid x)$**：
  採用 Dense Passage Retriever（DPR）雙編碼器架構：
  $$p_\eta(z \mid x) \propto \exp(d(z)^\top q(x))$$
  其中 $d(z) = \text{BERT}_d(z)$ 為文件向量，$q(x) = \text{BERT}_q(x)$ 為查詢向量。利用 Maximum Inner Product Search（MIPS）在次線性時間內檢索出前 $K$ 篇候選文件。
- **Generator（生成器）$p_\theta(y_i \mid x, z, y_{1:i-1})$**：
  採用 BART-large。輸入時直接將原始查詢 $x$ 與檢索文本 $z$ 進行字串拼接（concatenate）後輸入 BART 進行解碼生成。

### (2) 端到端聯合訓練（End-to-End Joint Training）
- 訓練資料僅需輸入/輸出對象 $(x_j, y_j)$，**完全不需要「應該檢索哪篇文件」的人工標註監督**（unsupervised retrieval training）。
- 目標函數為最小化負對數邊緣概似值（negative marginal log-likelihood）：$\sum_j -\log p(y_j \mid x_j)$。
- **運算考量與凍結策略**：為避免每次梯度更新都必須耗時重新計算 2100 萬篇維基文件的索引向量，訓練期間**固定（凍結）文件編碼器 $\text{BERT}_d$ 與文件索引**，僅聯合微調查詢編碼器 $\text{BERT}_q$ 與生成器 BART（Lewis et al., 2020）。

### (3) 推論解碼演算法（Decoding Procedures）
- **RAG-Token 解碼**：可視為標準自回歸轉移機率，直接套用標準 Beam Search 解碼。
- **RAG-Sequence 解碼**：由於機率無法直接拆解為標準的 per-token 概似值，論文設計兩種解碼方式：
  - **Thorough Decoding（徹底解碼）**：對前 $K$ 篇文件各自獨立執行 Beam Search 得到候選集合 $Y$，針對未在某文件 Beam 中產生的假說額外進行一次前向傳播計算機率，最後進行邊緣化加總。適用於短答案生成。
  - **Fast Decoding（快速解碼）**：對於較長的輸出序列，近似假設未在該文件 Beam 中出現的假說機率 $p_\theta(y \mid x, z_i) \approx 0$，避免額外的前向傳播開銷。

## 5. Example
Lewis et al.（2020）在四大知識密集型任務上的具體驗證：
1. **Open-domain Question Answering（開放領域問答基準表現）**：
   依據論文 Table 1 評測結果：
   - **Natural Questions（NQ）**：達到 SOTA（RAG-Seq 44.5、RAG-Token 44.1，高於 DPR 的 41.5 與 T5-11B+SSM 的 36.6）。在檢索文本未 verbatim 包含答案的情況下，RAG 仍能透過生成能力取得 11.8% 正確率（抽取式 extractive 模型得分為 0%）。
   - **WebQuestions（WQ）**：達到 SOTA（RAG-Token 45.5、RAG-Seq 45.2，高於 DPR 的 41.1 與 T5-11B+SSM 的 44.7）。
   - **CuratedTREC（CT）**：達到 SOTA（RAG-Seq 52.2、RAG-Token 50.0，高於 DPR 的 50.6 與 REALM 的 46.8）。
   - **TriviaQA（TQA）**：僅在 **TQA-Wiki / T5-comparable split** 達到相應 SOTA（RAG-Seq 68.0、RAG-Token 66.1，高於 T5-11B+SSM 的 60.5）；在標準 TriviaQA open-domain test split 上，RAG-Sequence 為 56.8（RAG-Token 為 55.2），整體低於 DPR 的 57.9。
2. **Abstractive Question Answering（生成式問答）**：
   - 評測基準：MS-MARCO NLG v2.1。
   - 結果：RAG-Sequence 在不依賴黃金標註段落的情況下，BLEU-1 與 ROUGE-L 皆比純參數化 BART 基線模型高出 2.6 分。
3. **Jeopardy Question Generation（益智問答題目生成）**：
   - 評測以實體反推事實說明的題目生成任務。人類評估顯示，在 42.7% 的案例中 RAG 比純 BART 更符合事實，且 RAG-Token 能有效結合不同文件的資訊。
4. **Index Hot-swapping（知識熱插拔實驗）**：
   - 使用 2016 年與 2018 年不同時期的維基索引進行比對。在詢問 82 位發生變動的世界各國領袖（如秘魯總統）時，載入 2016 索引時正確回答 2016 領袖的比例為 70%，抽換為 2018 索引後正確回答 2018 領袖的比例為 68%，證明無須重新訓練模型即可更新世界知識。

## 6. Limitations / Notes
根據 Lewis et al.（2020）論文指出的限制與觀察：
1. **文件編碼器凍結（Fixed Document Index）**：
   由於全量維基百科（21M 文件）建立向量索引成本極高，微調階段未更新文件編碼器，限制了檢索器對特定專業領域語意空間的進一步適應。
2. **原始架構邊界整理（Wiki 比較性分析）**：
   原始 RAG 屬於單向、單次檢索後生成的靜態管線，並未設計多輪迭代查詢重構（iterative query reformulation）或多輪互動式檢索回饋迴圈（multi-round retrieval loop）。
3. **外部知識偏差傳遞**：
   非參數化記憶源自外部語料（如維基百科），無法保證內容 100% 正確或無偏見，外部資料的有偏或錯誤資訊可能被生成器忠實繼承。
4. **Retrieval Collapse（附錄 F preliminary observation）**：
   作者在部分初步實驗（如 story generation）中觀察到 retriever 可能對不同輸入反覆取回相同文件，之後 generator 逐漸忽略 retrieved documents，使模型行為退化接近 BART。必須明確標示這是 Appendix F 的 preliminary experiment / observation，並非主文主要 benchmark 結論，不應被泛化為所有 RAG 任務必然發生的限制。

## 7. References
- **Lewis et al. (2020)**: Patrick Lewis, Ethan Perez, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Küttler, Mike Lewis, Wen-tau Yih, Tim Rocktäschel, Sebastian Riedel, Douwe Kiela. *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. Advances in Neural Information Processing Systems 33 (NeurIPS 2020), 2020. (arXiv:2005.11401v4, revised 12 Apr 2021). [03-rag.pdf](../raw/03-rag.pdf)
