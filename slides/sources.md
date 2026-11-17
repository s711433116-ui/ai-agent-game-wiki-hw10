# Slide Sources & Literature Registry

本文件依據 [AGENTS.md](../AGENTS.md) 規範，記錄所有已 Ingest 之文獻與來源 Metadata，作為 Wiki 與後續簡報內容的追溯基礎。

---

## Batch 1 Ingested Sources (第一批已處理來源)

### 1. A Survey on Large Language Model based Autonomous Agents
- **Title**: A Survey on Large Language Model based Autonomous Agents
- **Author / Organization**: Lei Wang, Chen Ma*, Xueyang Feng*, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen (*共同第一作者) / Gaoling School of Artificial Intelligence, Renmin University of China (中國人民大學高瓴人工智能學院)
- **Year**: 2024 (正式期刊出版年份: Frontiers of Computer Science, 2024; 另註明 arXiv 預印本: arXiv:2308.11432v7, revised 2025-03-02, DOI: 10.1007/s11704-024-40231-1)
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
- **Year**: 2023 (正式出版年份: NeurIPS 2023; 另註明 arXiv 預印本: arXiv:2302.04761v1, Feb 2023)
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

## Pending Sources (尚未 Ingest 之來源)
*依據規範，待後續批次逐一處理，目前嚴禁讀取或引用：*
- 外部來源 03–06（待後續批次開放）
- 內部來源：Project E.X.I.T. 設計文件（`../raw/project-exit-design.md`，待遊戲應用批次開放）
