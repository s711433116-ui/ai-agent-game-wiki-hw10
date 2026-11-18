# Slide Sources & Literature Registry

本文件依據 [AGENTS.md](../AGENTS.md) 規範，記錄所有已 Ingest 之文獻與來源 Metadata，作為 Wiki 與後續簡報內容的追溯基礎。

---

## Batch 1 Ingested Sources (第一批已處理來源)

### 1. A Survey on Large Language Model based Autonomous Agents
- **Title**: A Survey on Large Language Model based Autonomous Agents
- **Author / Organization**: Lei Wang, Chen Ma*, Xueyang Feng*, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen (*共同第一作者) / Gaoling School of Artificial Intelligence, Renmin University of China (中國人民大學高瓴人工智能學院)
- **Publication Year**: 2024
- **Venue / Publication Information**: Frontiers of Computer Science, 2024, 18(6): 186345. (DOI: 10.1007/s11704-024-40231-1; 另註明 arXiv 預印本: arXiv:2308.11432v7, revised 2025-03-02)
- **Original URL or file reference**: [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf) (arXiv:2308.11432)
- **Source type**: External Source (學術綜述論文 / Review Article)
- **Covered topics**:
  - AI Agent 基本架構與概念（Profile、Memory、Planning、Action 四大模組統一架構）
  - Profile 輪廓模組（手動設定、LLM 生成、資料集對齊）
  - Memory 記憶模組（結構：Unified、Hybrid；格式：自然語言、Embeddings、資料庫、結構化列表；操作：讀取、寫入、反思）
  - Planning 規劃模組（無回饋規劃：單路徑、多路徑、外部規劃器；有回饋規劃：環境回饋、人類回饋、模型內部回饋）
  - Action 行動模組（行動目標、行動產生機制、行動空間 [外部工具、內部知識]、行動影響）
  - Agent 能力獲取（微調機制 vs. 非微調機制 [Prompt Engineering、Mechanism Engineering]）
  - 多領域應用與評估策略（主觀評估、客觀評估與基準測試）
  - 當前挑戰（角色扮演能力、廣義人類對齊、提示詞強健性、幻覺問題、知識邊界、推論效率）

---

### 2. Toolformer: Language Models Can Teach Themselves to Use Tools
- **Title**: Toolformer: Language Models Can Teach Themselves to Use Tools
- **Author / Organization**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom / Meta AI Research, Universitat Pompeu Fabra
- **Publication Year**: 2023 (正式出版年份: NeurIPS 2023; 另註明 arXiv 預印本: arXiv:2302.04761v1, Feb 2023)
- **Venue / Publication Information**: Advances in Neural Information Processing Systems 36 (NeurIPS 2023), 2023.
- **Original URL or file reference**: [02-toolformer.pdf](../raw/02-toolformer.pdf) (arXiv:2302.04761)
- **Source type**: External Source (學術研究論文 / Research Paper)
- **Covered topics**:
  - Tool Use（語言模型工具使用）
  - 自監督工具學習機制（Self-supervised Tool Learning，無需大量人工標註）
  - 線性化 API 呼叫表示法（`<API> ac(ic) -> r </API>` 與特殊標記）
  - 三階段資料集擴增管線（API 採樣 Sample、API 執行 Execute、困惑度/損失過濾 Filter）
  - 核心語言模型微調（在保留通用語言建模能力下學習何時、如何呼叫工具）
  - 工具整合範例（計算機 Calculator、問答系統 Atlas QA、維基搜尋 Wikipedia Search / BM25、機器翻譯 NLLB、日曆 Calendar）
  - 規模法則（Scaling Laws，工具調用能力約在 775M 參數量浮現）
  - 當前技術限制（無法工具鏈接 Tool Chaining、無法互動式多輪探索、措辭敏感、特定工具樣本效率低、未考慮 API 運算成本）

---

## Batch 2 Ingested Sources (第二批已處理來源)

### 3. Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
- **Title**: Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
- **Author / Organization**: Patrick Lewis*, Ethan Perez*, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Küttler, Mike Lewis, Wen-tau Yih, Tim Rocktäschel, Sebastian Riedel, Douwe Kiela (*共同第一作者) / Facebook AI Research; University College London; New York University
- **Publication Year**: 2020 (正式出版年份: NeurIPS 2020; 另註明 arXiv 預印本: arXiv:2005.11401v4, revised 12 Apr 2021)
- **Venue / Publication Information**: Advances in Neural Information Processing Systems 33 (NeurIPS 2020), 2020.
- **Original URL or file reference**: [03-rag.pdf](../raw/03-rag.pdf) (arXiv:2005.11401)
- **Source type**: External Source (學術研究論文 / Research Paper)
- **Covered topics**:
  - 檢索增強生成（Retrieval-Augmented Generation, RAG）概念與架構
  - 參數化記憶（Parametric Memory: 預訓練 seq2seq 模型 BART）與非參數化記憶（Non-parametric Memory: DPR 檢索器 + 維基百科 21M 稠密向量索引）的混合模型
  - 兩大機率形式：RAG-Sequence Model（單一文件生成整序列）與 RAG-Token Model（每 token 邊緣化不同文件）
  - 端到端聯合訓練（Joint Training，無需顯式檢索監督）
  - 解碼策略（Thorough Decoding 與 Fast Decoding）
  - 知識密集型 NLP 基準測試（開放領域問答 NQ、TriviaQA、WebQuestions、CuratedTrec；生成問答 MS-MARCO NLG；事實查核 FEVER；題目生成 Jeopardy）
  - 非參數記憶熱插拔（Index Hot-swapping 更新世界知識）
  - 原始架構邊界（未包含多輪迭代查詢重構或動態互動檢索）
  - 檢索崩潰觀察（Appendix F preliminary observation: Retrieval Collapse）

---

### 4. LLM+P: Empowering Large Language Models with Optimal Planning Proficiency
- **Title**: LLM+P: Empowering Large Language Models with Optimal Planning Proficiency
- **Author / Organization**: Bo Liu*, Yuqian Jiang*, Xiaohan Zhang, Qiang Liu, Shiqi Zhang, Joydeep Biswas, Peter Stone (*共同第一作者) / Department of Computer Science, The University of Texas at Austin; Department of Computer Science, State University of New York at Binghamton; Sony AI
- **Publication Year**: 2023
- **Venue / Publication Information**: arXiv preprint arXiv:2304.11477v3 [cs.AI], 27 Sep 2023. (arXiv / CoRR)
- **Original URL or file reference**: [04-llm-plus-p.pdf](../raw/04-llm-plus-p.pdf) (arXiv:2304.11477)
- **Source type**: External Source (學術研究論文 / Research Paper)
- **Covered topics**:
  - LLM 在長程規劃（Long-horizon Planning）中的根本缺陷（缺乏 Functional Competence、前置條件推理與狀態追蹤不足）
  - 經典符號規劃形式化定義（Classical Planning Formulation: $\langle \mathcal{S}, s_{init}, \mathcal{S}^G, \mathcal{A}, f \rangle$）
  - PDDL（Planning Domain Definition Language）領域檔與問題檔
  - LLM+P 框架流程：LLM 透過單樣本上下文學習（1-shot In-context Learning）將自然語言轉譯為 Problem PDDL $\to$ 外部經典規劃器（Fast-Downward: SEQ-OPT-FDSS-1 與 LAMA）搜尋正確或最優規劃 $\to$ LLM 將符號計畫轉譯回自然語言/機器人執行動作
  - LLM+P 三大前置假設（對話觸發識別、專家提供之 Domain PDDL、單樣本上下文示範）
  - 7 大機器人規劃領域評測（Blocksworld, Barman, Floortile, Grippers, Storage, Termes, Tyreworld）與實體服務機器人整理家務展示
  - 技術限制（無法自發判斷調用時機、依賴人工定義之領域規則、生成錯誤導致問題無解）

---

## Pending Sources (尚未 Ingest 之來源)
*依據規範，待後續批次逐一處理，目前嚴禁讀取或引用：*
- 外部來源 05–06（`raw/05-reflexion.pdf`, `raw/06-voyager.pdf`，待後續批次開放）
- 內部來源：Project E.X.I.T. 設計文件（`../raw/project-exit-design.md`，待遊戲應用批次開放）
