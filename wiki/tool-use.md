# Tool Use（工具使用）

## 1. Overview
Tool Use（工具使用，指語言模型或 AI Agent 透過標準介面調用外部程式、API 或專家模組以完成特定任務的能力）是現代自主代理架構中 Action 模組的核心組成部分（Wang et al., 2024; Schick et al., 2023）。

儘管純文字大型語言模型（LLM）在少量樣本學習與文本生成展現出強大能力，但受限於自回歸神經網路的本質限制，往往面臨難以克服的瓶頸：無法取得訓練截止後的即時資訊（Inability to access up-to-date information）、缺乏時間演進覺察（Unawareness of the progression of time）、容易產生事實幻覺（Hallucination）、缺乏精確數值計算能力（Lack of mathematical skills for precise calculations），以及對低資源語言理解力不足等（Schick et al., 2023）。Tool Use 透過賦予模型與外部世界溝通的橋樑，有效克服上述限制。

## 2. Core Concept
### (1) 行動空間擴展（Action Space Expansion）
根據 Wang et al.（2024）綜述的分類，Agent 的行動空間可分為內部知識（Internal Knowledge）與外部工具（External Tools）。外部工具主要涵蓋三大類型（*註：以下具體工具案例為 Wang et al., 2024 綜述之回顧介紹，非本 Wiki 已 Ingest 原始文獻*）：
1. **APIs**：透過標準化程式介面連接外部服務（如 WebGPT 連接搜尋引擎、Gorilla 精準生成 API 呼叫參數、ToolBench 整合 16,000+ 真實 API、Toolformer 自主呼叫 API）。
2. **Databases & Knowledge Bases（資料庫與知識庫）**：允許 Agent 讀寫外部結構化資料（如 ChatDB 使用 SQL 語句操作符號資料庫記憶、MRKL 整合知識庫）。
3. **External Models（外部專家模型）**：調用領域特定模型以處理多模態或複雜任務（如 ViperGPT 調用 Codex 生成程式碼、ChemCrow 調用 17 種化學專家模型、MM-REACT 整合語音與圖像模型）。

### (2) Toolformer 的自監督工具學習概念
Schick et al.（2023）提出的 Toolformer 改變了傳統工具學習依賴大量人工標註（Human Annotation）或專屬於特定任務的提示詞（Task-specific Prompting）的困境，其核心概念為：
- **自監督學習（Self-supervised Tool Learning）**：僅需針對每個 API 提供極少數的人工撰寫範例（handful of demonstrations），由語言模型自主探索何時呼叫、呼叫哪個工具、傳遞何種輸入參數，以及如何將回傳結果融入未來的文本預測中。
- **線性化 API 呼叫格式（Linearized API Call Representation）**：
  API 呼叫表示為元組 $c = (a_c, i_c)$，其中 $a_c$ 為 API 名稱，$i_c$ 為輸入字串；$r$ 為回傳結果。
  - 不含結果的呼叫格式：$e(c) = \text{<API>} a_c(i_c) \text{</API>}$
  - 包含結果的呼叫格式：$e(c, r) = \text{<API>} a_c(i_c) \to r \text{</API>}$
  - *實作細節*：為避免擴充原有模型詞表，實際以 token `[`、`]`、`->` 代表特殊標記。

## 3. Why It Matters
- **下游任務表現之精確對照**：Schick et al.（2023）實驗顯示，經過 Toolformer 自監督工具學習微調的 6.7B 參數量 GPT-J 模型：
  - 在 **LAMA 事實補全**基準，以及 **ASDiv、SVAMP、MAWPS 等數學推理任務**上，Toolformer 能明顯優於參數量大 10 倍與 25 倍的 OPT（66B）與 GPT-3（175B）。
  - 但在 **WebQuestions、Natural Questions、TriviaQA 等一般開放問答任務**中，Toolformer 雖顯著優於同規模的 GPT-J baseline，整體表現仍低於參數量大許多的 GPT-3（175B）。（*註：文獻指出這主要受限於 BM25 檢索器的檢索精度，以及目前無法進行多輪互動查詢*）。
- **保留通用語言模型困惑度（Perplexity）**：在 WikiText 與 CCNet 的 perplexity 評估中，使用含 API call 的擴增資料進行微調，相較於使用相同文字資料但不加入 API call 的微調，並未造成額外的 perplexity 惡化（Schick et al., 2023）。
- **自主決策與泛化能力**：模型在面對輸入時能自行衡量是否需要外部工具協助，而非在預先設定好的固定流程中機械式呼叫。

## 4. How It Works
### (1) Toolformer 三階段資料建構與訓練流程（Schick et al., 2023）
1. **Sampling API Calls（採樣候選呼叫）**：
   - 給定純文字序列 $x = x_1, \dots, x_n$，透過包含示範的提示詞 $P(x)$ 鼓勵模型產生 API 呼叫。
   - 計算在位置 $i$ 生成 `<API>` 的條件機率 $p_i = p_M(\text{<API>} \mid P(x), x_{1:i-1})$。
   - 若 $p_i$ 高於閥值 $\tau_s$，保留前 $k$ 個候選位置，並在每個位置採樣最多 $m$ 個候選呼叫 $c_i$。
2. **Executing API Calls（執行外部工具）**：
   - 將採樣出的呼叫交由對應的外部工具（如執行 Python 腳本或檢索系統）實際執行，獲得文字結果 $r_i$。
3. **Filtering API Calls（困惑度/損失過濾）**：
   - 計算模型預測後續 token 的加權交叉熵損失（weighted cross-entropy loss）：
     $$L_i(z) = - \sum_{j=i}^n w_{j-i} \cdot \log p_M(x_j \mid z, x_{1:j-1})$$
   - 比較「提供工具呼叫與結果」的損失 $L_i^+ = L_i(e(c_i, r_i))$，與「不呼叫工具或未獲得結果」的最小損失 $L_i^- = \min(L_i(\varepsilon), L_i(e(c_i, \varepsilon)))$。
   - **保留準則**：僅當 $L_i^- - L_i^+ \ge \tau_f$（即工具及其結果使預測後續 token 的損失至少降低 $\tau_f$）時，才保留該 API 呼叫。
4. **Model Finetuning（微調語言模型）**：
   - 將篩選出的有用 API 呼叫交錯插入原始文字構成擴增資料集 $C^*$，以標準語言建模目標對原模型進行微調。

### (2) 推論解碼機制與實驗限制（Inference Decoding Mechanism）
- **中斷解碼流程**：模型執行常規貪婪解碼（greedy decoding），直至產生 `->` token（代表期待 API 回應）。此時暫停解碼，呼叫外部工具獲取結果並插入文本，補上 `</API>` 後恢復後續文字生成。
- **推論時的防迴圈限制**：
  > [!IMPORTANT]
  > 在架構與序列表示上，Toolformer 本身支援多個 API 呼叫在文本中穿插出現（論文定義中明確說明可對包含多個 API 呼叫的序列進行推論與微調）。然而在實驗推論時，為了避免模型陷入反覆呼叫 API 而不產生實質內容的無窮迴圈（API loop），作者人為施加了「每個輸入最多允許呼叫一次 API」的解碼限制（Schick et al., 2023）。這項限制屬於實驗評測時採用的防禦性推論策略，不應被描述為 Toolformer 模型架構本身必然只能呼叫一次。

### (3) 工具學習途徑對照（Wiki 比較性分析）
> [!NOTE]
> 「微調內化途徑」與「提示詞/上下文即時調用途徑」為**本 Wiki 針對不同研究典範所做的比較性整理與分析用語**，並非論文原創之正式分類名詞。

- **微調內化途徑（如 Toolformer）**：透過自監督學習產生資料並微調模型參數，將工具調用規則融入權重，模型在生成時自主發動（Schick et al., 2023）。
- **提示詞/上下文即時調用途徑（如 Wang et al., 2024 綜述所回顧之 ReAct、HuggingGPT 等）**：不修改底層模型參數，而是透過精細的上下文提示工程（In-Context Prompting）定義 API 規範，引導模型即時輸出呼叫指令。

## 5. Example
### Toolformer 實作的五大典型工具（Schick et al., 2023）
1. **Calculator（計算機）**：
   - 實作：簡易 Python 腳本，支援四則運算（`+`, `-`, `*`, `/`），結果四捨五入至小數點後兩位。
   - 範例：輸入 `Calculator(400 / 1400)` $\to$ 回傳 `0.29`。
2. **Question Answering（問答系統）**：
   - 實作：基於 Atlas 檢索增強模型，在 Natural Questions 上微調，處理簡單事實性問答。
   - 範例：輸入 `QA("Where was Joe Biden born?")` $\to$ 回傳 `Scranton`。
3. **Wikipedia Search（維基百科搜尋）**：
   - 實作：BM25 檢索器，索引 KILT Wikipedia dump，返回簡短文本段落。
   - 範例：輸入 `WikiSearch("Brown Act")` $\to$ 返回法案定義與核心摘要。
4. **Machine Translation System（機器翻譯系統）**：
   - 實作：NLLB（600M 參數）多語言翻譯模型，結合 fastText 自動偵測來源語言，一律翻譯為英文。
   - 範例：輸入 `MT("tortuga")` $\to$ 回傳 `turtle`。
5. **Calendar（日曆 API）**：
   - 實作：無須輸入參數，直接回傳當前日期（例如：`Today is Monday, January 30, 2023.`），為時間敏感問題提供時間錨點。

### 外部專家模型與資料庫案例（引自 Wang et al., 2024 綜述回顧，非本 Wiki 已 Ingest 原始論文）
- **ChemCrow**：調用 17 種化學專家模型進行有機合成與藥物研發。
- **ChatDB**：使用 SQL 語句操作符號資料庫記憶以提高計算與儲存精確度。

## 6. Limitations / Notes
根據 Schick et al.（2023）與 Wang et al.（2024）的嚴謹檢驗，當前 Tool Use 具備以下具體限制：
1. **無法進行工具鏈接（Inability to Chain Tools）**：
   Toolformer 無法將工具 A 的輸出直接傳遞給工具 B 作為輸入（例如：先查日曆取得年份，再將年份傳給搜尋引擎查詢總統）。原因在於訓練資料採樣時，各工具呼叫均獨立採樣，缺乏連續工具鏈的監督訊號（Schick et al., 2023）。
2. **缺乏互動式多輪調用（Non-interactive Tool Use）**：
   當前機制無法進行互動式反覆查詢（例如在搜尋結果不佳時重構 query 或翻閱下一頁），缺乏如人類瀏覽搜尋引擎般的雙向探索（Schick et al., 2023）。
3. **輸入措辭敏感度（Prompt Sensitivity）**：
   模型是否決定觸發工具呼叫，對輸入語句的細微文字變化高度敏感（Schick et al., 2023; Wang et al., 2024）。
4. **資料採樣效率低下（Sample Inefficiency）**：
   在建立擴增訓練集時，大量採樣呼叫無法通過損失過濾閥值（例如處理超過一百萬篇文檔，僅能篩選出數千個對計算機有實質幫助的有效樣本）（Schick et al., 2023）。
5. **忽略工具調用運算成本（Computational Cost Ignored）**：
   決定是否呼叫 API 時，尚未將外部工具運算的延遲與伺服器成本納入評估目標函數中（Schick et al., 2023）。
6. **規模門檻限制（Scaling Laws）**：
   Schick et al.（2023）針對 GPT-2 系列（124M, 355M, 775M, 1.6B）的實驗表明，工具調用能力直到模型達到約 775M 參數量以上才會顯現；小型模型即便微調也難以有效利用工具提升任務表現。
7. **資料缺口標示**：目前兩篇文獻著重於通用語言模型、NLP 基準測試與通用 API，尚未包含遊戲引擎特有之狀態查詢（Game State Inspection）與物理引擎互動 API。

## 7. References
- **Schick et al. (2023)**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom. *Toolformer: Language Models Can Teach Themselves to Use Tools*. Advances in Neural Information Processing Systems 36 (NeurIPS 2023), 2023. (arXiv:2302.04761v1, Feb 2023). [02-toolformer.pdf](../raw/02-toolformer.pdf)
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024. (arXiv:2308.11432v7, revised 2025-03-02, DOI: 10.1007/s11704-024-40231-1). [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
