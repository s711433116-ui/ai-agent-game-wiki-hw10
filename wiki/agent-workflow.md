# Agent Workflow（代理工作流程）

## 1. Overview
Agent Workflow（代理工作流程，指協調語言模型、記憶檢索、規劃決策、工具調用與多代理協同的結構化控制邏輯與執行管線）是實現複雜自主任務的核心（Wang et al., 2024; Schick et al., 2023; Lewis et al., 2020; Liu et al., 2023）。

傳統的單次提示（one-shot prompting）或單向文字生成，無法處理具有前後相依性、需要中間動態驗證或多角色分工的複雜任務。藉由構建結構化的 Agent Workflow，系統能將高階目標分解為有序的子任務階段，並依據環境反饋即時修正執行軌跡（Wang et al., 2024）。

> [!IMPORTANT]
> **文獻引用特別聲明**：
> 本頁面所提及之具體架構與演算法中：
> 1. **直接 Ingest 之原始研究**：`02-toolformer.pdf`（Schick et al., 2023，嵌入式工具流）、`03-rag.pdf`（Lewis et al., 2020，檢索生成子流程）、`04-llm-plus-p.pdf`（Liu et al., 2023，外部符號規劃子流程）。
> 2. **綜述回顧之次級引用**：ReAct、Reflexion、ChatDev、MetaGPT、ToT、GoT、RAP、DEPS、ReWOO 等方法，**其內容完全來自綜述文獻 `01-llm-agent-survey.pdf`（Wang et al., 2024）之回顧與歸納，本 Wiki 目前尚未直接 Ingest 這些個別方法的原始論文**。所有論點皆以相應文獻之描述為準。

## 2. Core Concept
根據目前已 Ingest 來源文獻之梳理，文獻支持的 Agent Workflow 主要涵蓋以下核心型態：

### (1) 單路徑串聯工作流程（Single-path Cascading Workflow）
任務被分解為單向依序連接的中間步驟，前一步驟的輸出作為後一步驟的輸入：
- **串聯推理管線**：如 Chain of Thought（CoT）與 Zero-shot-CoT，引導模型按部就班推理（引自 Wang et al., 2024 綜述介紹）。
- **計畫與觀察解耦流程（Decoupled Reasoning and Observation）**：以 ReWOO 為代表，將工作流程拆分為兩階段——第一階段由 Planner 生成完整計畫與預留變數，第二階段獨立執行外部觀察/工具調用，最後由 Solver 匯總結果，有效減少模型調用次數與 token 開銷（引自 Wang et al., 2024 綜述介紹）。

### (2) 多路徑分支與搜尋工作流程（Multi-path Branching & Search Workflow）
在決策節點產生多個可能候選，藉由樹狀或圖狀結構展開探索（引自 Wang et al., 2024 綜述介紹）：
- **投票共識流程**：如 Self-consistent CoT（CoT-SC），平行採樣多條推理路徑並透過多數決選出最優解。
- **樹狀/圖狀搜尋流程**：Tree of Thoughts（ToT）將每個思考節點視為中間步驟，利用廣度優先搜尋（BFS）或深度優先搜尋（DFS）進行評估與回溯；Graph of Thoughts（GoT）更進一步擴展為圖狀結構，支援節點合併與迴圈推導。
- **蒙地卡羅樹搜尋（MCTS）**：如 RAP，建立世界模型（World Model）模擬不同計畫的潛在效益，透過多輪 MCTS 迭代聚合出最終執行路徑。

### (3) 閉環回饋工作流程（Feedback Loop Workflows）
打破單向執行，在行動後引入評估與重規劃環節（引自 Wang et al., 2024 綜述介紹）：
- **環境感知回饋迴圈（Environmental Loop）**：如 ReAct 透過「思考-行動-觀察」（Thought-Act-Observation）三元組交替推進，根據外部工具或環境的即時 Observation 調整下一輪的 Thought；Voyager 與 Ghost 則依據程式執行回傳之錯誤訊息進行動態重規劃。
- **自我反思優化迴圈（Model Feedback / Self-Refine Loop）**：如 Self-Refine 與 Reflexion，採取「輸出 $\to$ 評估反饋 $\to$ 修正」的迭代循環，直到產出符合標準為止。

### (4) 多代理協作工作流程（Multi-Agent Collaborative Workflow）
多個具備不同 Profile 的 Agent 依照特定協作協議分工合作（引自 Wang et al., 2024 綜述介紹）：
- **階段瀑布型流程（Waterfall / Stage-based Chain）**：如 ChatDev 將軟體開發生命週期拆解為需求分析、編碼、測試等階段，每階段由特定代理（如 CEO、程式設計師、測試員）依序對話傳遞成品。
- **群體辯論與共識流程（Debate / Consensus Mechanism）**：多個代理對同一問題提出個別方案，相互檢驗相異處並迭代修正，直至達成共識。

### (5) 嵌入式工具調用工作流程（Inline Tool Invocation Workflow）
Schick et al.（2023）在 Toolformer 中定義了語言模型在 token 生成過程中的中斷與恢復流程：
- 生成文本 $\to$ 產生特定標記（如 `[`、`QA(`） $\to$ 預測至箭頭符號（`->`）時**暫停解碼** $\to$ 調用外部 API $\to$ 將回傳結果與閉合標記（`]`）插入序列 $\to$ **恢復解碼**生成後續文字。

### (6) 可嵌入 Agent Workflow 的外部符號規劃子流程（External Symbolic Planning Sub-process）
Liu et al.（2023）在 LLM+P 中定義了自然語言轉譯與外部符號求解器結合的特定管線子流程（*註：LLM+P 本身為特定問題求解管線，可作為 Agent 規劃模組的一種實作子流程，非通用 Agent 完整架構*）：
- 使用者自然語言需求 $\to$ LLM（單樣本提示）將問題轉譯為結構化 Problem PDDL $\to$ 外部經典規劃器（Fast-Downward）讀取 Domain PDDL 與 Problem PDDL 搜尋符號計畫 $\to$ LLM 將符號計畫轉譯回自然語言或機器人底層動作。

### (7) 可嵌入 Agent Workflow 的檢索－生成子流程（Retrieve-then-Generate Sub-process）
Lewis et al.（2020）在 RAG 中定義了端到端可微分的知識檢索增強子流程（*註：RAG 本身為 NLP 知識生成模型，可作為 Agent 記憶與知識檢索的底層組件，非完整 Autonomous Agent*）：
- 輸入查詢 $x \to$ 稠密向量檢索器（DPR）於非參數化記憶（MIPS 索引）中快速檢索 Top-$K$ 相關文本區塊 $z \to$ 生成器（BART）將 $x$ 與 $z$ 拼接作為輸入 $\to$ 依據 RAG-Sequence 或 RAG-Token 邊緣化機率解碼輸出最終目標文字 $y$。

## 3. Why It Matters
- **化解長程任務（Long-horizon Tasks）的脆弱性**：在複雜環境中，一開始擬定的初始計畫極難完美，一旦環境狀態改變或發生未預期錯誤，靜態流程便告崩潰；導入包含 Feedback 與動態 Re-planning 的工作流程能賦予代理適應性（Wang et al., 2024）。
- **結合符號規劃以提升長程任務可行性**：單靠神經網路推論無法保證長程動作序列的前置條件完全滿足；透過嵌入 LLM+P 這類外部符號規劃子流程，在 Problem PDDL 正確且求解成功的條件下，能借助經典規劃器生成高可行性或最優計畫，但端到端表現仍取決於前端自然語言轉譯的準確性（Liu et al., 2023）。
- **控制計算成本與提升可靠性**：如 ReWOO 的解耦流程能減少反覆呼叫大模型的 token 消耗；而部分多代理研究透過辯論或相互檢核機制改善輸出可靠性與事實性，但效果仍取決於任務、模型與協作設計（Wang et al., 2024）。

## 4. How It Works
### (1) 開環（Open-loop）vs. 閉環（Closed-loop）工作流程機制（Wiki 比較性分析）
> [!NOTE]
> 「開環流程」與「閉環流程」（以及「外部分層排程視角」與「內部自回歸中斷視角」）為**本 Wiki 為了對照不同系統架構與控制特徵所採用的比較性整理與分析用語**，並非論文作者原本提出的正式分類名詞。

- **開環流程（無回饋，Planning without Feedback）**：
  - 代表方法：CoT、Zero-shot CoT、Toolformer、LLM+P（單趟轉譯執行子流程）、RAG（單趟檢索生成子流程）。
  - 機制：模型依賴既有先驗一次性展開推導、調用工具或交由規劃器產生完整序列。
  - **Toolformer 限制釐清**：在 Toolformer 實驗中，作者為避免推論時產生無窮 API loop 而設定「每個輸入最多呼叫 1 次 API」的限制，這是**實驗推論層級的防禦性策略**，而非架構本身無法多重調用（Schick et al., 2023）。開環流程整體優點為推論快速且實作單純，但無法根據執行失敗即時調整策略。
- **閉環流程（有回饋，Planning with Feedback）**：
  - 代表方法：ReAct、DEPS、SayPlan、Reflexion（引自 Wang et al., 2024 綜述介紹）。
  - 機制：執行後由環境感知模組或批評器（Critic）產生反饋（如數值分數、環境狀態圖、詳細自然語言診斷）；Planning 模組接收反饋後啟動重規劃（Re-planning），形成動態修正迴圈。

### (2) 流程架構差異對照（Wiki 比較性分析）
- **外部分層排程視角（綜合系統層次，如 Wang et al., 2024 綜述所歸納與 Liu et al., 2023）**：將工作流程視為外部排程系統（如高階規劃器負責任務拆解，低階執行器負責具體控制），強調多角色分工與各模組間的資訊傳遞鏈。
- **內部自回歸中斷/解碼視角（模型計算層次，如 Schick et al., 2023 與 Lewis et al., 2020）**：Toolformer 將工具執行視為語言模型自身解碼過程中的同步「中斷-填充」流程；RAG 則在模型架構內部透過潛在變數邊緣化完成「檢索-拼接-生成」流程，未建立外部排程狀態機。

## 5. Example
*註：案例 1–3 引自 Wang et al. (2024) 綜述論文之回顧整理，非本 Wiki 已 Ingest 原始論文；案例 4–5 來自直接 Ingest 之原始文獻：*
1. **ReAct 工作流程（引自 Wang et al., 2024 綜述）**：
   - 階段 1（Thought）：Agent 根據目前目標分析現狀，思考需要哪些資訊。
   - 階段 2（Act）：輸出標準工具呼叫指令（如 `Search[主題]`）。
   - 階段 3（Observation）：外部環境執行工具並回傳結果。
   - 階段 4（Next Thought）：Agent 根據 Observation 判斷是否已足夠回答，若不足則重回階段 1 發動下一輪檢索。
2. **ReWOO 解耦工作流程（引自 Wang et al., 2024 綜述）**：
   - Planner 階段：生成包含變數佔位符的整體計畫藍圖（如 `#E1 = Search[Q1]`, `#E2 = Calculator[#E1]`）。
   - Worker 階段：逐一向外部工具查詢填補變數。
   - Solver 階段：將所有填補完畢的觀察結果交給最終模型生成結論，避免各步驟反覆提示整個歷史紀錄。
3. **ChatDev 軟體生命週期流程（引自 Wang et al., 2024 綜述）**：
   - 設計階段（Design）：CEO 與 CPO 對話確定產品需求。
   - 編碼階段（Coding）：CTO 與 Programmer 對話完成原始碼編寫。
   - 測試階段（Testing）：Reviewer 與 Programmer 對話進行程式碼審查與除錯。
4. **LLM+P 符號規劃子流程（Liu et al., 2023）**：
   - 使用者提出家務需求（如「整理咖啡桌與邊桌垃圾」） $\to$ GPT-4 依據 1-shot 範例轉譯為 Problem PDDL $\to$ Fast-Downward 規劃器結合預先定義之居家 Domain PDDL 搜尋可行/最優動作序列 $\to$ GPT-4 轉譯為機器人移動與夾取執行步驟。
5. **RAG 檢索增強生成子流程（Lewis et al., 2020）**：
   - 輸入事實查詢 $\to$ DPR 計算查詢向量並於 FAISS 維基索引中檢索 Top-$K$ 文本 $\to$ BART 將查詢與文本拼接 $\to$ 依邊緣化機率自回歸解碼輸出答案。

## 6. Limitations / Notes
根據現有文獻明確指出的限制，以及當前批次資料的覆蓋邊界：
1. **工具鏈接（Tool Chaining）能力缺失**：Toolformer 明確承認其目前工作流程無法串接工具（即 Tool A 輸出直接輸入 Tool B），因為其 API 呼叫在訓練資料中皆為獨立採樣產生（Schick et al., 2023）。
2. **缺乏多輪互動探索（Non-interactive）**：Toolformer 與 RAG 原始流程均屬於單次檢索/呼叫觸發，無法像人類一樣在搜尋引擎中翻頁或多次重新下關鍵字（Schick et al., 2023; Lewis et al., 2020）。
3. **外部規劃管線依賴前提完備性**：LLM+P 的管線若要成功，仰賴人類專家預先給定完整無誤的領域規則（Domain PDDL），且若 LLM 轉譯出的 Problem PDDL 出現細微狀態遺漏，外部求解流程將直接宣告無解（Liu et al., 2023）。
4. **推論延遲累積（Latency Overhead）**：多步驟串聯、多路徑搜尋（如 ToT、RAP）以及多代理反覆對話的工作流程，會使推論時間急遽增加（Wang et al., 2024）。
5. **文獻覆蓋限制與未包含內容**：
   - 本頁內容嚴格受限於已 Ingest 之 `01-llm-agent-survey.pdf`、`02-toolformer.pdf`、`03-rag.pdf` 與 `04-llm-plus-p.pdf`。
   - 目前文獻尚未提及遊戲專用之「行為樹（Behavior Tree）」、「有限狀態機（FSM）」或「AI Director」等遊戲領域特定的工作流程整合方式（待後續遊戲應用批次文獻提供）。

## 7. References
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024. (arXiv:2308.11432v7, revised 2025-03-02, DOI: 10.1007/s11704-024-40231-1). [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
- **Schick et al. (2023)**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom. *Toolformer: Language Models Can Teach Themselves to Use Tools*. Advances in Neural Information Processing Systems 36 (NeurIPS 2023), 2023. (arXiv:2302.04761v1, Feb 2023). [02-toolformer.pdf](../raw/02-toolformer.pdf)
- **Lewis et al. (2020)**: Patrick Lewis, Ethan Perez, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Küttler, Mike Lewis, Wen-tau Yih, Tim Rocktäschel, Sebastian Riedel, Douwe Kiela. *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. Advances in Neural Information Processing Systems 33 (NeurIPS 2020), 2020. [03-rag.pdf](../raw/03-rag.pdf)
- **Liu et al. (2023)**: Bo Liu, Yuqian Jiang, Xiaohan Zhang, Qiang Liu, Shiqi Zhang, Joydeep Biswas, Peter Stone. *LLM+P: Empowering Large Language Models with Optimal Planning Proficiency*. arXiv preprint arXiv:2304.11477v3 [cs.AI], 27 Sep 2023. [04-llm-plus-p.pdf](../raw/04-llm-plus-p.pdf)
- **次級引用聲明（Secondary Citations via Survey）**：
  - ReAct (Yao et al., 2022 / ICLR 2023)、ReWOO (Xu et al., 2023)、ChatDev (Qian et al., 2023)、ToT (Yao et al., 2023)、GoT (Besta et al., 2023)、RAP (Hao et al., 2023)、DEPS (Wang et al., 2023)、Self-Refine (Madaan et al., 2023)、Reflexion (Shinn et al., 2023) 等方法，本專案目前皆是經由 Wang et al. (2024) 綜述論文獲取其回顧性論點，尚未直接 Ingest 其原始研究論文。
