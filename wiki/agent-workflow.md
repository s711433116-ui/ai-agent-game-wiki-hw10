# Agent Workflow（代理工作流程）

## 1. Overview
Agent Workflow（代理工作流程，指協調語言模型、記憶檢索、規劃決策、工具調用與多代理協同的結構化控制邏輯與執行管線）是實現複雜自主任務的核心（Wang et al., 2024; Schick et al., 2023; Lewis et al., 2020; Liu et al., 2023; Shinn et al., 2023; Wang et al., 2023）。

傳統的單次提示（one-shot prompting）或單向文字生成，無法處理具有前後相依性、需要中間動態驗證或多角色分工的複雜任務。藉由構建結構化的 Agent Workflow，系統能將高階目標分解為有序的子任務階段，並依據環境反饋即時修正執行軌跡（Wang et al., 2024）。

> [!IMPORTANT]
> **文獻引用特別聲明**：
> 本頁面所提及之具體架構與演算法中：
> 1. **直接 Ingest 之原始研究（Primary Citations）**：
>    - `02-toolformer.pdf`（Schick et al., 2023，嵌入式工具流）
>    - `03-rag.pdf`（Lewis et al., 2020，檢索生成子流程）
>    - `04-llm-plus-p.pdf`（Liu et al., 2023，外部符號規劃子流程）
>    - `05-reflexion.pdf`（Shinn et al., 2023，語言增強自我反思試錯迴圈）
>    - `06-voyager.pdf`（Wang et al., 2023，具身終身學習課綱、技能庫與自我驗證迴圈）
> 2. **綜述回顧之次級引用（Secondary Citations via Survey）**：
>    - ReAct、ChatDev、MetaGPT、ToT、GoT、RAP、DEPS、ReWOO、Self-Refine 等方法，**其內容完全來自綜述文獻 `01-llm-agent-survey.pdf`（Wang et al., 2024）之回顧與歸納，本 Wiki 目前尚未直接 Ingest 這些個別方法的原始論文**。所有論點皆以相應文獻之描述為準。
> 3. **內部專案設計文件（Internal Design Case）**：
>    - Project E.X.I.T.（`project-exit-design.md`，遊戲系統多代理協作應用案例）。標示為 `Source: Project E.X.I.T. Design Document`，僅代表內部專案之設計規劃（Planned），**不能作為外部學術研究之實證證據**。

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
打破單向執行，在行動後引入評估與重規劃環節：
- **環境感知回饋迴圈（Environmental Loop）**：如 ReAct 透過「思考-行動-觀察」（Thought-Act-Observation）三元組交替推進，根據外部工具或環境的即時 Observation 調整下一輪的 Thought（引自 Wang et al., 2024 綜述）；Voyager 則依據程式執行之環境反饋與錯誤訊息進行動態除錯與程式碼迭代重整（Wang et al., 2023）。
- **自我反思優化迴圈（Model Feedback / Self-Refine Loop）**：如 Self-Refine（引自 Wang et al., 2024 綜述）採取「輸出 $\to$ 評估反饋 $\to$ 修正」的單次迭代；Reflexion 則將失敗反思寫入跨試驗情節記憶，形成多輪試錯優化迴圈（Shinn et al., 2023）。

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

### (8) 語言增強試錯反思工作流程（Verbal Reinforcement & Trial-and-Error Workflow）
Shinn et al.（2023）在 Reflexion 中定義了基於文字回饋與情節記憶的多輪試驗優化工作流程：
- 試驗階段：Actor 根據當前記憶與任務生成動作軌跡 $\tau_t$ $\to$ Evaluator 評估軌跡產出成敗或獎勵 $r_t$ $\to$ 若失敗，Self-Reflection 模型分析 $\{\tau_t, r_t\}$ 生成自然語言反思摘要 $sr_t$ $\to$ 將 $sr_t$ 追加至長期情節記憶緩衝區 $\text{mem}$（受滑動視窗 $\Omega \approx 1-3$ 限制） $\to$ 重置環境並展開下一輪試驗（Trial $t+1$），由 Actor 結合歷史反思重新規劃與執行。

### (9) 具身終身學習與技能演化工作流程（Embodied Lifelong Learning & Skill Evolution Workflow）
Wang et al.（2023）在 VOYAGER 中定義了結合自動課綱、技能庫與自我驗證的開放世界具身控制流程：
- **課綱提議（Curriculum）**：Automatic Curriculum 根據代理即時狀態（背包、環境、生物）與歷史探索進度，自主提議由淺入深的新任務目標。
- **技能檢索（Skill Retrieval）**：依據任務規劃與環境反饋，自 Skill Library 向量資料庫檢索 Top-5 相關技能程式碼。
- **迭代提示控制迴圈（Iterative Prompting Loop，至多 4 輪嘗試）**：
  - GPT-4 生成/微調控制程式碼 $\to$ Mineflayer 於 Minecraft 執行程式碼並收集環境反饋（`bot.chat()`）與解譯器語法/執行報錯 $\to$ GPT-4 Self-Verification Critic 評判任務是否真正完成並提供 Critique。
- **技能固化與演化（Skill Commitment）**：若驗證成功，由 GPT-3.5 生成技能描述並計算 Embedding 索引，將程式碼存入技能庫，並更新課綱完成進度；若 4 輪皆失敗，課綱記錄失敗並切換至其他任務。

### (10) 遊戲系統應用工作流程（Project E.X.I.T. Application Workflow，Internal Design Case）
> [!NOTE]
> 本段整理自 `project-exit-design.md`，標示為 `Source: Project E.X.I.T. Design Document`。來源只支援專案設計規劃，不作為一般 AI Agent 效能的學術證據；所有內容均屬 Planned，且文件沒有說明個別功能的實作完成度。

設計文件目前可直接支持的工作流程資訊如下：
- **Core Loop**：基地準備 $\to$ 接受任務 $\to$ 進入地圖 $\to$ 探索 / 搜刮 / 戰鬥 $\to$ AI 動態事件 $\to$ 撤離 $\to$ 結算 $\to$ 返回基地。
- **AI Director**：預計讀取 `Player HP`、`Inventory`、`Location`、`Mission progress`、`Remaining time`、`Base resources`，再決定 `Dynamic quest`、`Optional event`、`Risk warning` 或 `NPC response`。
- **Tool Use**：規劃用於取得 Game State、Inventory、Map、Mission 等實際遊戲資料。
- **RAG**：規劃讓 NPC 查詢世界觀、地圖、物品與角色知識。
- **Planning**：規劃用於多步驟複合任務。
- **Reflection**：規劃檢查任務是否可完成、是否與 Game State 衝突、獎勵是否合理。
- **Agent Workflow**：文件明示區分 AI Director、Quest Agent、NPC Agent 與 Game System，但**未提供完整的 agent-to-agent 執行順序、訊息格式或 orchestration state machine**。
- **Deterministic boundary**：角色移動、碰撞、HP 扣除、掉落機率、背包容量等明確規則由一般遊戲程式處理，不交給 LLM 即時決定。

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
  - 代表方法：ReAct、DEPS、SayPlan（引自 Wang et al., 2024 綜述介紹）；Reflexion 則由本專案已直接 Ingest 的 Shinn et al. (2023) 原始論文支持。
  - 機制：執行後由環境感知模組或批評器（Critic）產生反饋（如數值分數、環境狀態圖、詳細自然語言診斷）；Planning 模組接收反饋後啟動重規劃（Re-planning），形成動態修正迴圈。

### (2) 流程架構差異對照（Wiki 比較性分析）
- **外部分層排程視角（綜合系統層次，如 Wang et al., 2024 綜述所歸納與 Liu et al., 2023）**：將工作流程視為外部排程系統（如高階規劃器負責任務拆解，低階執行器負責具體控制），強調多角色分工與各模組間的資訊傳遞鏈。
- **內部自回歸中斷/解碼視角（模型計算層次，如 Schick et al., 2023 與 Lewis et al., 2020）**：Toolformer 將工具執行視為語言模型自身解碼過程中的同步「中斷-填充」流程；RAG 則在模型架構內部透過潛在變數邊緣化完成「檢索-拼接-生成」流程，未建立外部排程狀態機。

## 5. Example
*註：案例 1–3 引自 Wang et al. (2024) 綜述論文之回顧整理；案例 4–7 來自直接 Ingest 之原始研究；案例 8 為 Project E.X.I.T. 內部設計文件之應用整理：*
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
6. **Reflexion 語言增強試錯反思流程（Shinn et al., 2023）**：
   - 在 AlfWorld 決策任務中，Trial 1 因動作順序不當導致在 desk 2 找不到檯燈而失敗 $\to$ Self-Reflection 模型產生反思「desk 1 上有檯燈，應先去 desk 1 取檯燈再找杯子」並寫入長期情節記憶 $\to$ Trial 2 Actor 讀取該反思，直接前往 desk 1 取得檯燈，順利完成任務。
7. **VOYAGER 具身迭代提示與技能合成流程（Wang et al., 2023）**：
   - VOYAGER 的 Automatic Curriculum 可提出 `Craft 1 stone pickaxe` 等任務；在 iterative prompting 的 Figure 5 中，另一個程式生成案例展示模型如何根據 environment feedback 發現材料不足並補充 planks/sticks，以及根據 execution error `No item named acacia_axe` 將不存在的 `acacia_axe` 修正為可用的 wooden axe。這些回饋會被加入下一輪 GPT-4 prompt，直到 self-verification 判定成功或最多完成 4 輪 code generation。一旦驗證成功，新技能程式碼即被存入向量資料庫以供後續複合任務檢索呼叫。
8. **Project E.X.I.T. 遊戲系統應用（Internal Design Case）**：
   - 設計文件明示的 Core Loop 為「基地準備 $\to$ 接受任務 $\to$ 進入地圖 $\to$ 探索 / 搜刮 / 戰鬥 $\to$ AI 動態事件 $\to$ 撤離 $\to$ 結算 $\to$ 返回基地」。
   - AI Director 預計讀取 `Player HP`、`Inventory`、`Location`、`Mission progress`、`Remaining time`、`Base resources`，並決定 `Dynamic quest`、`Optional event`、`Risk warning` 或 `NPC response`。
   - Tool Use、RAG、Planning、Reflection 與 Agent Workflow 的用途依設計文件規劃；角色移動、碰撞、HP 扣除、掉落機率與背包容量仍由一般遊戲程式處理。
   - **來源沒有提供具體事件內容、數值門檻或完整的角色間執行時序，因此此處不自行補寫。**

## 6. Limitations / Notes
根據現有文獻明確指出的限制，以及當前批次資料的覆蓋邊界：
1. **工具鏈接（Tool Chaining）能力缺失**：Toolformer 明確承認其目前工作流程無法串接工具（即 Tool A 輸出直接輸入 Tool B），因為其 API 呼叫在訓練資料中皆為獨立採樣產生（Schick et al., 2023）。
2. **缺乏多輪互動探索（Non-interactive）**：Toolformer 與 RAG 原始流程均屬於單次檢索/呼叫觸發，無法像人類一樣在搜尋引擎中翻頁或多次重新下關鍵字（Schick et al., 2023; Lewis et al., 2020）。
3. **外部規劃管線依賴前提完備性**：LLM+P 的管線若要成功，仰賴人類專家預先給定完整無誤的領域規則（Domain PDDL），且若 LLM 轉譯出的 Problem PDDL 出現細微狀態遺漏，外部求解流程將直接宣告無解（Liu et al., 2023）。
4. **推論延遲累積（Latency Overhead）**：多步驟串聯、多路徑搜尋（如 ToT、RAP）以及多代理反覆對話的工作流程，會使推論時間急遽增加（Wang et al., 2024）。
5. **文獻覆蓋限制與來源邊界**：
   - 本頁內容整合 6 篇已 Ingest 之外部學術來源（`01` 至 `06`）與 1 份內部專案設計文件（`project-exit-design.md`）。
   - 外部學術文獻提供通用 Agent Workflow 範式與具身探索實證；Project E.X.I.T. 僅代表自建遊戲系統之設計意圖與架構規劃（Planned），不能作為外部學術研究之實證證據。

## 7. References
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024, 18(6): 186345. (DOI: 10.1007/s11704-024-40231-1; arXiv:2308.11432v7, revised 2025-03-02). [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
- **Schick et al. (2023)**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom. *Toolformer: Language Models Can Teach Themselves to Use Tools*. Advances in Neural Information Processing Systems 36 (NeurIPS 2023), 2023. (arXiv:2302.04761v1, Feb 2023). [02-toolformer.pdf](../raw/02-toolformer.pdf)
- **Lewis et al. (2020)**: Patrick Lewis, Ethan Perez, Aleksandra Piktus, Fabio Petroni, Vladimir Karpukhin, Naman Goyal, Heinrich Küttler, Mike Lewis, Wen-tau Yih, Tim Rocktäschel, Sebastian Riedel, Douwe Kiela. *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*. Advances in Neural Information Processing Systems 33 (NeurIPS 2020), 2020. [03-rag.pdf](../raw/03-rag.pdf)
- **Liu et al. (2023)**: Bo Liu, Yuqian Jiang, Xiaohan Zhang, Qiang Liu, Shiqi Zhang, Joydeep Biswas, Peter Stone. *LLM+P: Empowering Large Language Models with Optimal Planning Proficiency*. arXiv preprint arXiv:2304.11477v3 [cs.AI], 27 Sep 2023. [04-llm-plus-p.pdf](../raw/04-llm-plus-p.pdf)
- **Shinn et al. (2023)**: Noah Shinn, Federico Cassano, Edward Berman, Ashwin Gopinath, Karthik Narasimhan, Shunyu Yao. *Reflexion: Language Agents with Verbal Reinforcement Learning*. arXiv preprint arXiv:2303.11366v4 [cs.AI], 10 Oct 2023. [05-reflexion.pdf](../raw/05-reflexion.pdf)（註：後續正式發表於 NeurIPS 2023，正式版作者名單未列 Edward Berman）。
- **Wang et al. (2023)**: Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar, Chaowei Xiao, Yuke Zhu, Linxi Fan, Anima Anandkumar. *VOYAGER: An Open-Ended Embodied Agent with Large Language Models*. arXiv preprint arXiv:2305.16291v2 [cs.AI], 19 Oct 2023. [06-voyager.pdf](../raw/06-voyager.pdf)（註：後續正式發表於 TMLR, March 2024）。
- **Project E.X.I.T. Design Document**: *Project E.X.I.T. Design Document*. Internal Project Design Document. [project-exit-design.md](../raw/project-exit-design.md)
- **次級引用聲明（Secondary Citations via Survey）**：
  - ReAct (Yao et al., 2022 / ICLR 2023)、ReWOO (Xu et al., 2023)、ChatDev (Qian et al., 2023)、ToT (Yao et al., 2023)、GoT (Besta et al., 2023)、RAP (Hao et al., 2023)、DEPS (Wang et al., 2023)、Self-Refine (Madaan et al., 2023) 等方法，本專案目前皆是經由 Wang et al. (2024) 綜述論文獲取其回顧性論點，尚未直接 Ingest 其原始研究論文。