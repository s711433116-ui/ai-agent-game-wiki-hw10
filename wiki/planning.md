# Planning（規劃機制）

## 1. Overview
在 AI Agent 架構中，Planning（規劃機制）是指代理將複雜的長程目標（Long-horizon Goals）分解為一系列具備因果相依性與可行性之子任務或動作序列的能力（Wang et al., 2024; Liu et al., 2023）。

儘管現有大型語言模型（LLM）展現出優異的零樣本常識推理能力，但在面對嚴謹的機器人任務與長程規劃時，純 LLM（LLM-as-Planner）往往無法可靠地輸出可執行的可行計畫（feasible plans）。Liu et al.（2023）提出的 **LLM+P** 框架，透過將 LLM 的自然語言轉譯能力與經典符號規劃器（Classical / Symbolic Planners）的搜尋演算法相結合，以提升 LLM 處理複雜規劃問題的準確性與規劃品質。

> [!IMPORTANT]
> **架構定位聲明**：
> LLM+P 是將外部符號規劃器結合至語言模型的**特定代表性研究方法**，不應被泛化為所有 AI Agent 的唯一或通用 Planning 架構。在廣義 Agent 體系中，規劃機制尚包含無回饋單/多路徑推理與各類環境反饋閉環（如 Wang et al., 2024 綜述所述）。

## 2. Core Concept
### (1) 語言能力 vs. 功能性規劃能力（Linguistic vs. Functional Competence）
Liu et al.（2023）引用認知科學觀點（Mahowald et al., 2023），指出 LLM 在規劃問題中的核心瓶頸：
- **語言能力（Linguistic Competence，knowing how to say things）**：LLM 極度擅長理解與生成流暢的自然語言，並具備類似「機器翻譯」的能力，能將自然語言描述轉譯為其他結構化格式（如 PDDL）。
- **功能性能力（Functional Competence，knowing what to say / how the world works）**：LLM 欠缺對實體世界因果運作的深層邏輯理解。在面對長程規劃時，LLM 難以嚴密推理動作的前置條件（Preconditions）、無法持續追蹤複雜狀態（如積木世界中的 ON 與 CLEAR 狀態、酒吧調酒前需先清洗酒杯），亦無法處理嚴格的空間幾何拓撲限制（如地面磁磚相鄰塗色規則）。

### (2) 經典符號規劃問題形式化（Classical Planning Problem Formulation）
依據經典自動化規劃理論（Liu et al., 2023），一個規劃問題 $P$ 在數學上定義為五元組：
$$\langle \mathcal{S}, s_{\text{init}}, \mathcal{S}^G, \mathcal{A}, f \rangle$$
- $\mathcal{S}$：描述世界狀態的有限且離散的狀態空間（State Space）。
- $s_{\text{init}} \in \mathcal{S}$：世界的初始狀態（Initial State）。
- $\mathcal{S}^G \subset \mathcal{S}$：目標狀態集合（Goal States），通常由一組必須同時滿足的目標條件指定。
- $\mathcal{A}$：符號動作集合（Symbolic Actions）。
- $f$：底層狀態轉移函數（State Transition Function），輸入當前狀態與動作，輸出後續狀態。
- **解的定義**：規劃問題的解為一個符號動作序列 $\pi = \langle a_1, a_2, \dots, a_N \rangle$，使得各動作之前置條件在執行當下依序滿足，並在執行完畢後達成目標狀態 $\mathcal{S}^G$。

### (3) PDDL 規劃領域定義語言（Planning Domain Definition Language）
PDDL 是經典符號規劃問題的標準編碼格式（Liu et al., 2023），通常分為兩份獨立檔案：
1. **Domain PDDL 檔案（領域規則定義）**：提供世界底層物理規則的提煉表示（lifted representation）。定義狀態述詞（Predicates）以及動作庫 $\mathcal{A}$ 各動作的前置條件與效果（即狀態轉移函數 $f$）。此檔案與具體任務無關（task-agnostic）。
2. **Problem PDDL 檔案（具體問題定義）**：提供具體情境的物件清單（Objects）、初始狀態 $s_{\text{init}}$ 與目標條件 $\mathcal{S}^G$。

### (4) 外部經典規劃器（Classical Symbolic Planner）的角色與保證範疇
經典符號規劃器（如 Fast-Downward）使用高效的狀態空間搜尋演算法：
- **規劃器的數學保證**：多數經典規劃器具備可靠性（Soundness，保證輸出之計畫必能在符號系統中正確執行）與完備性（Completeness，若存在可行計畫必能找出）。特定搜尋設定（如 `SEQ-OPT-FDSS-1`）在給定足夠運算時間下，能保證找到動作步數最短或代價最小的最優計畫（Optimal Plan）。
- **端到端管線保證範疇的釐清**：
  > [!WARNING]
  > 必須明確區分「符號規劃器本體」與「LLM+P 端到端管線」：當 Domain PDDL 與 LLM 產生的 Problem PDDL 完全正確，且使用具備相應 soundness / completeness / optimality 性質的 planner configuration，並在資源限制內成功求解時，符號規劃器可以提供正確或最優計畫；**這不代表整個 LLM+P end-to-end pipeline 具有 100% 成功保證**。若前端 LLM 在生成 Problem PDDL 時遺漏關鍵條件，整個管線仍會失敗（Liu et al., 2023）。

## 3. Why It Matters
- **化解「LLM-as-Planner」的普遍失敗**：Liu et al.（2023）實驗證實，直接讓 GPT-4 等先進大模型充當規劃器（LLM-as-Planner），即便使用複雜的上下文提示詞或 Tree of Thoughts（ToT）搜尋，在複雜長程任務中幾乎無法生成可行的正確計畫（多數領域成功率為 0% 至 20%）。
- **人機介面與符號推理的優勢結合**：LLM+P 結合了兩者的長處——LLM 提供自然語言人機互動介面與 1-shot 格式轉換能力；經典規劃器則在正確輸入下提供嚴密邏輯搜尋能力（Liu et al., 2023）。
- **無須微調模型**：LLM+P 完全不調整 LLM 本身的參數，直接利用現成模型透過上下文學習發揮效能。

## 4. How It Works
### (1) LLM+P 執行管線（Liu et al., 2023）
1. **輸入接收**：機器人接收使用者以自然語言描述的新規劃問題 $P$。
2. **LLM 轉譯 Problem PDDL**：
   - 藉由單樣本上下文學習（1-shot In-context Learning），提供 LLM 一組簡單範例的「自然語言描述與對應 Problem PDDL」。
   - LLM 將複雜輸入 $P$ 轉譯為標準 Problem PDDL 格式檔案。
3. **經典規劃器求解**：
   - 將由人類專家預先給定的 Domain PDDL 檔與 LLM 生成的 Problem PDDL 檔一併輸入外部規劃器（如 Fast-Downward）。
   - 規劃器執行搜尋，輸出符號化規劃方案（PDDL Plan）。
4. **輸出轉換**：
   - LLM 將 PDDL Plan 重新翻譯為自然語言說明，或轉換為實體機器人的動作控制指令序列。

### (2) LLM+P 的三大核心假設（Assumptions）
Liu et al.（2023）明確指出 LLM+P 的正常運作依賴三項先決條件：
1. 機器人具備從對話中自發判斷「何時該觸發 LLM+P 管線」的能力。
2. 該問題領域已由人類專家撰寫完成高品質且不變的 Domain PDDL 檔案。
3. 提供給 LLM 進行 In-context Learning 的簡單範例 Problem PDDL 必須存在且格式正確。

### (3) Planning 機制來源觀點對照（Wiki 比較性分析）
> [!NOTE]
> 本段落將本專案直接 Ingest 之原始研究 `04-llm-plus-p.pdf` 與綜述文獻 `01-llm-agent-survey.pdf` 的 Planning 分類進行結構化對照，為本 Wiki 的比較性分析整理。

- **外部符號規劃範式（Primary Research: Liu et al., 2023）**：
  LLM 僅擔任「翻譯器（Translator）」，將推理與狀態轉移交給符號計算系統（External Classical Planner），在 Liu et al. (2023) 所測試的 IPC-derived planning benchmark domains 中，LLM+P 在多數受測領域的可行性或最優計畫成功率高於直接使用 LLM-as-Planner 的 baseline。
- **多維度規劃範式架構（Survey Review: Wang et al., 2024）**：
  Wang et al.（2024）綜述文獻將 Planning 模組分為：
  - **Planning without Feedback**：單路徑串聯（CoT, Zero-shot-CoT, ReWOO）、多路徑分支搜尋（CoT-SC, ToT, GoT, RAP）、調用外部規劃器（LLM+P 即被歸列於此，同類包含 LLM-DP、CO-LLM）。
  - **Planning with Feedback**：環境回饋閉環（ReAct, Voyager）、人類回饋（Inner Monologue）、模型內部自省反饋（Self-Refine, Reflexion）。
- **觀點綜合說明**：在規則明確、前置條件嚴格的符號環境中，外部規劃器展現了極佳的求解優勢；但此結論受限於 IPC 類型的離散規劃基準，不應被過度推論為在所有規劃情境中符號系統皆必然全面超越神經網路。

## 5. Example
### (1) 7 大機器人規劃基準測試（Liu et al., 2023）
Liu et al.（2023）在源自國際規劃競賽（IPC）的 7 個機器人場景中，各生成 20 個任務（共 140 個規劃問題）進行嚴格評測：
- **Blocksworld（積木世界）**：移動積木堆疊至指定構型。純 GPT-4 成功率僅 15%（無法追蹤狀態），而 LLM+P 成功率達 90%。
- **Barman（機器人調酒師）**：依客戶訂單調配雞尾酒。純 GPT-4 成功率為 0%（常忘記洗杯），LLM+P 達到 100% 滿足解（最優解 20%）。
- **Floortile（地磚彩繪）**：機器人不可踩在已上色的磁磚上。純 GPT-4 與 ToT 成功率皆為 0%，LLM+P 成功率亦為 0%——**原因在於 LLM 在轉譯 Problem PDDL 時漏掉了地磚相鄰連通關係，直接導致符號規劃器因輸入缺陷而判定無解，凸顯了前端翻譯對整體管線成敗的決定性影響**。
- **Grippers（機械夾爪搬運）**：雙夾爪機器人在房間之間搬移物品。純 GPT-4 成功率 35%，LLM+P 達到 95% 最優解（100% 滿足解）。
- **Storage（倉儲堆高機）**：移動貨箱至倉庫。純 GPT-4 成功率 0%，LLM+P 成功率達 85%。
- **Termes（建築機器人）**：機器人搬運方塊並爬上方塊建造高塔。純 GPT-4 為 0%，LLM+P 最優解成功率為 20%。
- **Tyreworld（換輪胎）**：拆裝螺母、充氣、依序收納工具。純 GPT-4 成功率 15%，LLM+P 達 90% 滿足解（最優解 10%）。

### (2) 實體居家服務機器人清理示範（Real-robot Tidy-up Task）
在真實居家環境中，使用者要求機械手臂機器人「將咖啡桌上的芥末醬瓶放進茶水間，並將邊桌上的空湯罐丟進回收桶」。
- **純 LLM（LLM-as-P）產出次優計畫**：機器人先拿芥末醬直奔茶水間，再折返拿湯罐，總移動代價高達 31。
- **LLM+P 產出最優計畫**：經典規劃器發現邊桌與回收桶位於前往茶水間的中途，規劃出「先拿芥末醬 $\to$ 帶至邊桌放下 $\to$ 拿取湯罐丟入回收桶 $\to$ 重新抓取芥末醬 $\to$ 前往茶水間放置」的最優方案，總代價僅 22（Liu et al., 2023）。

## 6. Limitations / Notes
根據 Liu et al.（2023）的研究分析，當前符號增強規劃方法具備以下明確限制：
1. **依賴人工定義 Domain PDDL**：
   LLM+P 目前無法自主從環境中學習或自動生成領域物理規則，必須由具備專業知識的人類專家預先提供完善的 Domain PDDL 檔案。
2. **缺乏自發觸發識別機制（Lack of Auto-detection）**：
   論文未設計由 LLM 自動判斷何種提示詞該導入 LLM+P 管線的機制，目前均仰賴人為直接指定流程。
3. **Problem PDDL 轉譯錯漏的致命性**：
   在失敗案例中，LLM 在 1-shot 轉譯時若漏掉特定初始條件（例如在 Floortile 中漏寫了磁磚之間的相鄰關係，或 Blocksworld 中漏寫積木在桌上），將導致符號規劃器直接判定無解。
4. **運算逾時限制**：
   在極大狀態空間（如 Barman 或 Termes）中，若追求嚴格的最優解（Optimal Alias `SEQ-OPT-FDSS-1`），規劃器可能在 200 秒時間限制內逾時，必須退回次優滿足解（Satisficing Alias `LAMA`）。
5. **資料缺口標示**：目前兩篇來源（Survey 與 LLM+P）探討的均為靜態符號領域或通用離散機器人動作，尚未涵蓋動態電玩遊戲中的即時連續控制與行為樹整合。

## 7. References
- **Liu et al. (2023)**: Bo Liu, Yuqian Jiang, Xiaohan Zhang, Qiang Liu, Shiqi Zhang, Joydeep Biswas, Peter Stone. *LLM+P: Empowering Large Language Models with Optimal Planning Proficiency*. arXiv preprint arXiv:2304.11477v3 [cs.AI], 27 Sep 2023. [04-llm-plus-p.pdf](../raw/04-llm-plus-p.pdf)
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024. (arXiv:2308.11432v7, revised 2025-03-02). [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
- **Mahowald et al. (2023)**: Kyle Mahowald, Anna A. Ivanova, Idan A. Blank, Nancy Kanwisher, Joshua B. Tenenbaum, Evelina Fedorenko. *Dissociating Language and Thought in Large Language Models: a Cognitive Perspective*. arXiv:2301.06627, 2023. (引自 Liu et al., 2023 轉引).
