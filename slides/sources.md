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

## Batch 3 Ingested Sources (第三批已處理來源)

### 5. Reflexion: Language Agents with Verbal Reinforcement Learning
- **Title**: Reflexion: Language Agents with Verbal Reinforcement Learning
- **Author / Organization**:
  - Ingested Source Authors (arXiv version): Noah Shinn, Federico Cassano, Edward Berman, Ashwin Gopinath, Karthik Narasimhan, Shunyu Yao / Northeastern University; Massachusetts Institute of Technology; Princeton University
  - Formal Publication Note: 後續正式發表於 NeurIPS 2023 之論文作者名單未列 Edward Berman；本專案因實際 Ingest 之來源為 05-reflexion.pdf (arXiv v4)，故保留原始 PDF 之作者紀錄，並在此標註版本差異。
- **Publication Year**: 2023
- **Venue / Publication Information**:
  - Ingested Source Version: arXiv preprint arXiv:2303.11366v4 [cs.AI], 10 Oct 2023. (arXiv / CoRR; 論文首頁標示: "Preprint. Under review.")
  - Formal Publication: Advances in Neural Information Processing Systems 36 (NeurIPS 2023), Main Conference Track, 2023.
- **Original URL or file reference**: [05-reflexion.pdf](../raw/05-reflexion.pdf) (arXiv:2303.11366)
- **Source type**: External Source (學術研究論文 / Research Paper)
- **Covered topics**:
  - 語言增強學習（Verbal Reinforcement Learning，透過語言反饋強化代理政策，不更新模型神經網路參數）
  - 三大核心模型：Actor ($M_a$ 行動生成)、Evaluator ($M_e$ 評估評分)、Self-Reflection ($M_{sr}$ 自我反思語言反饋)
  - 記憶機制：短期軌跡記憶（Short-term Trajectory Memory）與長期情節經驗記憶（Long-term Episodic Memory，滑動視窗 $\Omega \approx 1-3$）
  - 語意梯度（Semantic Gradient）：將稀疏/標量/二元獎勵訊號放大為具體自然語言反思，指導後續試驗（trial）
  - 基準測試與評估：AlfWorld（決策）、HotPotQA（多跳推理）、HumanEval / MBPP / LeetcodeHardGym（程式碼生成與自建單元測試）
  - 技術限制：依賴大模型自省能力（較大較強模型之湧現特性）、可能陷入局部最佳解（local minima）、對需要高探索度任務（如 WebShop）效果有限、無形式化成功保證

---

### 6. VOYAGER: An Open-Ended Embodied Agent with Large Language Models
- **Title**: VOYAGER: An Open-Ended Embodied Agent with Large Language Models
- **Author / Organization**: Guanzhi Wang*1,2, Yuqi Xie3, Yunfan Jiang*4, Ajay Mandlekar*1, Chaowei Xiao1,5, Yuke Zhu1,3, Linxi "Jim" Fan†1, Anima Anandkumar†1,2 (*共同第一作者, †共同指導) / 1NVIDIA, 2Caltech, 3UT Austin, 4Stanford, 5UW Madison
- **Publication Year**: 2023 (arXiv preprint) / 2024 (Formal journal publication)
- **Venue / Publication Information**: Publication: arXiv preprint arXiv:2305.16291v2 [cs.AI], 19 Oct 2023; formally published in Transactions on Machine Learning Research (TMLR), March 2024. (專案主頁: https://voyager.minedojo.org)
- **Original URL or file reference**: [06-voyager.pdf](../raw/06-voyager.pdf) (arXiv:2305.16291)
- **Source type**: External Source (學術研究論文 / Research Paper)
- **Covered topics**:
  - 具身終身學習代理（Embodied Lifelong Learning Agent）在開放世界（Minecraft / MineDojo）之探索與技能學習
  - 三大核心組件：
    1. Automatic Curriculum（自動課綱：利用 GPT-4 驅動由易到難、最大化探索的新奇度搜尋，以底部向上開展）
    2. Skill Library（技能庫：以可執行的 JavaScript / Mineflayer 程式碼作為動作空間，以向量資料庫索引技能描述 Embedding，支援組合合成與緩解 [alleviates] 災難性遺忘）
    3. Iterative Prompting Mechanism（迭代提示機制：結合環境反饋 Environment Feedback、程式執行錯誤 Execution Errors、GPT-4 自我驗證 Critic Self-Verification）
  - 程式碼作為動作空間（Code as Action Space: 時間延展性、可解釋性、可組合性）
  - 基準評測（MineDojo）：道具探索數量（3.3x 於基線）、地圖導航距離（2.3x）、科技樹解鎖速度（最多 15.3x，唯一解鎖鑽石工具）、新世界零樣本泛化（Zero-shot Generalization）
  - 限制與觀察：GPT-4 API 運算成本高昂（比 GPT-3.5 貴 15 倍）、程式碼幻覺（如虛構物品、錯認無效燃料）、驗證器失誤、缺乏視覺感知（依賴 Mineflayer 文字與結構化狀態，非像素級控制）

---

## Internal Sources (內部專案來源)

### 7. Project E.X.I.T. Design Document
- **Title**: Project E.X.I.T. Design Document
- **Author / Owner**: Not specified in current design document
- **Date / Version**: Not specified in current design document
- **Original URL or file reference**: [project-exit-design.md](../raw/project-exit-design.md)
- **Source type**: Internal Project Design Document / Application Case Source
- **Implementation status**: Design-stage / Planned; the document does not state which individual functions are already implemented.
- **Covered topics**:
  - 專案核心循環（基地準備 → 接受任務 → 進入地圖 → 探索 / 搜刮 / 戰鬥 → AI 動態事件 → 撤離 → 結算 → 返回基地）
  - AI Director 規劃架構（預計讀取 Player HP、Inventory、Location、Mission progress、Remaining time、Base resources，動態決定任務、事件、警報或 NPC 回應）
  - 規劃導入之 AI Agent 技術（Tool Use 取得遊戲資料、RAG 知識查詢、Planning 複合任務拆解、Reflection 任務與狀態驗證、Agent Workflow 系統分工）
  - 系統邏輯邊界（角色移動、碰撞、HP 扣除、掉落機率、背包容量等明確規則由一般遊戲程式處理，不交給 LLM 即時決定）
  - 專案實作狀態（明確標示全案屬於設計規劃階段，不代表所有功能已完成實作）
- **Evidence boundary**:
  `This source supports only Project E.X.I.T. design descriptions and implementation intentions. It is not academic evidence for general AI Agent effectiveness.`（本文件僅支援 Project E.X.I.T. 的自建應用案例與設計意圖描述，不能作為一般性 AI Agent 研究成效的學術證據）
- **Not specified in current design document**: model/provider, Prompt design, Tool schema, RAG implementation details, Planning algorithm, Reflection execution loop, exact inter-agent orchestration, trigger thresholds, evaluation metrics.

---

## Pending Sources (尚未 Ingest 之來源)
*本專案規劃之 6 篇外部學術來源與 1 份內部設計文件已全數完成 Ingest。*
- **No pending planned sources.**
