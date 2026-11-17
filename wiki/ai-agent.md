# AI Agent 基本概念

## 1. Overview
在人工智慧領域中，Autonomous Agent（自主代理，指能在環境中自主感知、決策並採取行動以達成特定目標的系統）長期以來是學術界與產業界的研究核心。根據 Franklin 與 Graesser（1997）的經典定義，自主代理是指「處於環境之中且作為環境一部分的系統，它能感知該環境並隨著時間對其採取行動，以追求自身的目標，並對其未來的感知產生影響」（引自 Wang et al., 2024）。

隨著具備海量網路知識的大型語言模型（Large Language Models, LLM）展現出達到人類水準智慧的潛力，以 LLM 作為核心控制器（Central Controller）建構的「LLM-based Autonomous Agent」（以大型語言模型為基底的自主代理）已成為實現通用人工智慧（Artificial General Intelligence, AGI）的重要研究路徑（Wang et al., 2024）。

## 2. Core Concept
### (1) LLM-based Agent 與傳統強化學習 Agent 的差異
根據 Wang et al.（2024）的綜述分析，LLM-based Agent 與傳統基於強化學習（Reinforcement Learning, RL）的代理存在本質差異：
- **環境與先驗知識**：傳統 RL Agent 通常假設在封閉、隔離且高度受限的環境中學習，依賴簡單的啟發式策略函數（heuristic policy functions），其學習過程與人類在多元開放世界中的認知差異甚大；相較之下，LLM-based Agent 具備廣泛的內部世界知識（internal world knowledge），即便未在特定領域資料上訓練，也能做出知情且合理的行動（informed actions）。
- **互動與可解釋性**：LLM-based Agent 原生支援自然語言互動介面（natural language interfaces），具備高度靈活性與可解釋性，便於人類理解其決策邏輯並進行引導。

### (2) 統一架構框架（Unified Architecture Framework）
Wang et al.（2024）提出了一套統整現有研究的四模組架構（Unified Agent Architecture）：
1. **Profile Module（輪廓模組）**：指定代理的角色（Role）、性格（Personality）與社會背景（Social Information）。生成方式包含人工手動指定（Handcrafting Method）、LLM 自動生成（LLM-generation Method）與真實資料集對齊（Dataset Alignment Method）。
2. **Memory Module（記憶模組）**：儲存從環境感知到的資訊與自身歷程。結構上分為僅模擬短期記憶的 Unified Memory（直接放入提示詞 context window），以及結合短期快取與長期向量儲存的 Hybrid Memory；支援讀取（Reading，依據時間近度 recency、關聯度 relevance、重要度 importance 評分）、寫入（Writing，處理重複與溢位）與反思（Reflection，將經驗抽象化為高層次洞見）三大操作。
3. **Planning Module（規劃模組）**：將複雜任務拆解為子任務。分為「無回饋規劃」（Planning without Feedback，包含單路徑推理 Single-path Reasoning、多路徑推理 Multi-path Reasoning、調用外部規劃器 External Planner）與「有回饋規劃」（Planning with Feedback，引入環境反饋 Environmental Feedback、人類反饋 Human Feedback 或模型自我反饋 Model Feedback）。
4. **Action Module（行動模組）**：負責將高階決策轉換為具體輸出，直接與環境互動。涵蓋行動目標（Action Goal）、行動產生依據（Action Production，來自記憶檢索或計畫跟隨）、行動空間（Action Space，外部工具與內部知識）以及行動影響（Action Impact，改變環境、更新內部狀態、觸發新行動）。

### (3) 能力獲取途徑（Capability Acquisition）
Agent 的架構如同硬體，而任務技能與經驗則為軟體資源。能力獲取分為兩大策略（Wang et al., 2024）：
- **微調方式（With Fine-tuning）**：透過人類標註資料集、LLM 生成資料集或真實世界資料集調整模型參數，適用於開源模型。
- **無微調方式（Without Fine-tuning）**：
  - **Prompt Engineering（提示工程）**：透過精細的自然語言提示詞激發模型潛在能力。
  - **Mechanism Engineering（機制工程）**：設計特殊的演進機制，包含試錯演練（Trial-and-error）、群體辯論（Crowd-sourcing）、經驗積累（Experience Accumulation）與自主進化（Self-driven Evolution）。

## 3. Why It Matters
- **跳脫靜態問答（QA）限制**：傳統 LLM 主要以被動的問答（Question-Answering）形式運作；然而建構自主代理必須具備自主感知、角色扮演、主動規劃並在動態環境中持續進化的能力（Wang et al., 2024）。
- **補足純神經網路的固有缺陷**：LLM 雖然擁有廣泛常識，但在事實準確性、即時資訊獲取與複雜運算上存在天然短板；自主代理架構透過結合記憶、工具使用與自我反思，大幅拓展了大型模型的應用邊界（Schick et al., 2023; Wang et al., 2024）。

## 4. How It Works
### (1) 模組間之協同運作流程
根據 Wang et al.（2024）的框架設計：
- **Profile 驅動**：Profile 模組定義了代理的定位，直接影響 Memory 模組儲存與關注的重點，並規範 Planning 模組的推理邏輯與目標設定。
- **雙向記憶與規劃**：代理在動態環境中，透過 Memory 模組檢索過去類似經歷（Recency, Relevance, Importance 評分），輔助 Planning 模組制定或調整長程計畫。
- **決策落地與反饋推進**：Planning 產生的分步計畫指導 Action 模組執行（如呼叫外部 API 或輸出對話）。Action 產生的結果作用於環境，並透過環境感知回傳至 Memory（寫入新經驗）或直接提供給 Planning 進行動態重規劃（Re-planning）。

### (2) 來源間架構觀點對照（Wiki 比較性分析）
> [!NOTE]
> 以下「模組化分工視角」與「端到端內化視角」為**本 Wiki 為了協助初學者對照不同研究切入點所採用的比較性整理與分析用語**，並非論文作者原本提出的正式分類名詞。

- **模組化分工視角（綜合系統層次，如 Wang et al., 2024）**：主張以 LLM 作為核心大腦，外部構建明確的 Profile、Memory、Planning 與 Action 等模組，並透過提示工程或外部機制（Mechanism Engineering）形成閉環控制系統。
- **端到端內化視角（模型本體層次，如 Toolformer / Schick et al., 2023）**：不依賴分離式的記憶與規劃外掛模組，而是認為自主決策能力（例如「何時呼叫工具」、「呼叫何種工具」）可以直接透過自監督學習（Self-supervised Learning）微調內化進語言模型的 token 預測機率中，使模型在生成文字時自主發動行動。
- **文獻差異說明**：此差異源於研究範疇的不同。Wang et al.（2024）探討的是宏觀的綜合代理系統（如模擬人類社會行為或機器人任務），需長程規劃與龐大記憶體系；而 Toolformer 聚焦於語言模型底層工具調用能力的獲得，展現了「將代理行為微調內化至模型本體」的可行性。

## 5. Example
Wang et al.（2024）綜述中彙整了多個代表性 Agent 案例（*註：以下具體模型為 Wang et al., 2024 綜述文獻之回顧介紹，本 Wiki 目前尚未直接 Ingest 其原始論文*）：
- **Generative Agents（Park et al., 2023，引自 Wang et al., 2024）**：在虛擬小鎮中模擬 25 位居民的社交生活。每個居民透過手動設定 Profile（如身分、目標、人際關係），運用 Hybrid Memory 儲存日常事件並定期進行高層次反思（Reflection，例如將「連續撰寫論文」等事件抽象化為「致力於學術研究」的洞見），並擬定每日計畫與即時互動。
- **Minecraft 遊戲代理（如 Voyager、GITM、DEPS，引自 Wang et al., 2024）**：在虛擬方塊世界中，代理結合環境回饋（如程式執行報錯、障礙物感知）即時修正規劃，並將成功技能存入長期記憶庫供後續調用。
- **軟體開發協作代理（如 ChatDev，引自 Wang et al., 2024）**：將軟體開發週期拆分為不同角色（CEO、程式設計師、測試員），各代理依據對話記憶互相通訊並完成任務。

## 6. Limitations / Notes
根據 Wang et al.（2024）在文獻中明確指出的當前挑戰，以及本批次資料的覆蓋範圍限制：
1. **角色扮演真實性（Role-playing Capability）**：LLM 雖然能模仿常見職業，但對於冷門角色、新興角色或複雜的人類認知心理特質，模擬精度仍有欠缺，且缺乏深層自我覺察。
2. **廣義人類對齊（Generalized Human Alignment）**：在模擬環境（如社會模擬）中，代理若要如實反映人類多樣性，有時必須允許模擬非正向特質（例如探索社群負面行為擴散）；但現有模型普遍被微調成單一正向價值觀，如何在安全對齊與真實模擬間取得平衡為未解問題。
3. **提示詞強健性（Prompt Robustness）**：當代理架構包含多個提示模組時，微小提示變更可能導致行為顯著偏差，且跨模型通用性不足。
4. **幻覺問題（Hallucination）**：模型可能產生虛假但看似自信的輸出，在程式碼生成或科學實驗中可能引發嚴重後果。
5. **知識邊界難以限制（Knowledge Boundary）**：LLM 內部具有超越一般人類常識的龐大網路知識，在模擬特定普通人行為時難以完全屏蔽其預訓練先驗。
6. **推論效率瓶頸（Efficiency）**：自回歸生成的慢速推論使多輪規劃與反覆記憶檢索運算開銷龐大。
7. **當前資料不足提醒**：本頁內容嚴格受限於 `01-llm-agent-survey.pdf` 與 `02-toolformer.pdf`，目前尚未涵蓋專門的遊戲引擎介面規範，亦不可使用未 Ingest 之來源補齊。

## 7. References
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024. (arXiv:2308.11432v7, revised 2025-03-02, DOI: 10.1007/s11704-024-40231-1). [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
- **Schick et al. (2023)**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom. *Toolformer: Language Models Can Teach Themselves to Use Tools*. Advances in Neural Information Processing Systems 36 (NeurIPS 2023), 2023. (arXiv:2302.04761v1, Feb 2023). [02-toolformer.pdf](../raw/02-toolformer.pdf)
- **Franklin & Graesser (1997)**: Stan Franklin and Art Graesser. *Is it an Agent, or just a Program?: A Taxonomy for Autonomous Agents*. Third International Workshop on Agent Theories, Architectures, and Languages, Springer, 1997. (引自 Wang et al., 2024 綜述轉引).
