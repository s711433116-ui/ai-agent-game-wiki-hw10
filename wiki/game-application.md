# Game Application（遊戲系統應用）

> [!IMPORTANT]
> **來源性質與證據邊界特別聲明**：
> 本頁面整合兩類性質完全不同之來源，嚴格禁止混淆：
> 1. **外部學術研究案例（External Academic Research Case）**：**Voyager in Minecraft（Wang et al., 2023）**，可用於說明具身終身學習代理的研究架構與實驗結果。
> 2. **自建專案應用案例（Internal Project Application Case）**：**Project E.X.I.T. 設計文件（`project-exit-design.md`）**。此來源標示為 `Source: Project E.X.I.T. Design Document`，僅支援本專案的設計規劃與實作意圖，**不可作為一般性 AI Agent 有效性的學術研究證據**。文件並明確聲明：目前內容屬於設計規劃，不代表所有功能已完成實作。

---

## 1. Overview
本頁以兩個性質不同的案例說明生成式 AI 與遊戲系統的結合方式：

- **Research Case: Voyager in Minecraft**：VOYAGER 將 LLM 作為具身代理的一部分，在 Minecraft / MineDojo 中透過 Automatic Curriculum、Skill Library 與 Iterative Prompting Mechanism 進行自主探索與技能累積（Wang et al., 2023）。
- **Internal Application Case: Project E.X.I.T.**：Project E.X.I.T. 設計文件規劃 AI Director、Quest Agent、NPC Agent 與 Game System 的分工，並規劃 Tool Use、RAG、Planning、Reflection 與 Agent Workflow 等技術。此處只描述設計文件實際支持的內容，不代表系統已完成實作（Source: Project E.X.I.T. Design Document）。

---

## 2. Core Concept
### (1) 開放世界沙盒案例 vs. Project E.X.I.T. 核心循環
- **Voyager / Minecraft**：研究聚焦於開放世界沙盒環境中的自主探索、技能發現與累積（Wang et al., 2023）。
- **Project E.X.I.T.**：設計文件給出的核心循環為：

  $$\text{基地準備} \to \text{接受任務} \to \text{進入地圖} \to \text{探索 / 搜刮 / 戰鬥} \to \text{AI 動態事件} \to \text{撤離} \to \text{結算} \to \text{返回基地}$$

  Source: Project E.X.I.T. Design Document.

### (2) Voyager 的具身終身學習 vs. Project E.X.I.T. 的 AI Director
- **VOYAGER**：以 Automatic Curriculum、Skill Library 與 Iterative Prompting Mechanism 支援開放世界探索與技能重用（Wang et al., 2023）。
- **Project E.X.I.T. AI Director**：設計文件規劃 AI Director 讀取 `Player HP`、`Inventory`、`Location`、`Mission progress`、`Remaining time`、`Base resources`，再決定 `Dynamic quest`、`Optional event`、`Risk warning` 或 `NPC response`。文件沒有進一步規定觸發門檻、模型種類或各輸出的具體內容。

### (3) 確定性遊戲邏輯與 LLM / Agent 的責任邊界
> [!NOTE]
> **Wiki design interpretation based on Project E.X.I.T. Design Document**：
> Project E.X.I.T. 的設計文件明確將部分遊戲規則保留給一般遊戲程式，並把 LLM / Agent 規劃集中於狀態讀取、知識查詢、任務規劃、反思檢查與高階輸出。以下只整理文件明示的責任，不補入文件未定義的遊戲規則。

| 項目 | 一般遊戲程式 / Game System | AI / Agent 設計 |
|---|---|---|
| **角色移動** | 由一般遊戲程式處理 | 不交給 LLM 即時決定 |
| **碰撞** | 由一般遊戲程式處理 | 不交給 LLM 即時決定 |
| **HP 扣除** | 由一般遊戲程式處理 | AI Director 可讀取 `Player HP` 作為輸入 |
| **掉落機率** | 由一般遊戲程式處理 | 不交給 LLM 即時決定 |
| **背包容量** | 由一般遊戲程式處理 | AI Director / Tool Use 可讀取 `Inventory` |
| **遊戲資料取得** | 提供實際 Game State / Inventory / Map / Mission 資料 | Tool Use 規劃用於取得這些資料 |
| **AI Director 輸出** | 文件未指定底層執行細節 | 規劃決定 Dynamic quest、Optional event、Risk warning 或 NPC response |

Source: Project E.X.I.T. Design Document.

---

## 3. Why It Matters
- **研究案例的意義**：Voyager 顯示 LLM-based agent 可在 Minecraft 中透過自動課綱、可重用技能庫與多輪環境回饋完成長期探索；相關結論僅適用於其研究設定與實驗結果（Wang et al., 2023）。
- **內部案例的教學價值（Wiki application mapping）**：Project E.X.I.T. 提供一個可將 Tool Use、RAG、Planning、Reflection 與 Agent Workflow 對照到遊戲系統設計的案例；這是架構映射，不是實證成效。
- **責任邊界**：Project E.X.I.T. 文件明確保留角色移動、碰撞、HP 扣除、掉落機率與背包容量等規則給一般遊戲程式處理，可用來說明「高階 AI 功能」與「確定性遊戲規則」如何在設計層級分離（Source: Project E.X.I.T. Design Document）。

---

## 4. How It Works
### (1) VOYAGER 具身學習管線（Wang et al., 2023）
1. **Automatic Curriculum**：GPT-4 根據目前狀態與探索進度提出新任務。
2. **Skill Library**：以可執行 JavaScript 程式碼保存技能，並透過文字描述的 Embedding 進行檢索與重用。
3. **Iterative Prompting Mechanism**：利用環境回饋、程式執行錯誤與 Self-Verification Critic 反覆修正程式碼，成功後再將技能存入技能庫。

### (2) Project E.X.I.T. AI Director（Source: Project E.X.I.T. Design Document）
> [!IMPORTANT]
> **Implementation status**：以下內容全部屬於設計規劃（Planned）。設計文件沒有逐項宣告哪些功能已完成，因此不可將個別模組寫成已實作。

**Planned inputs**
- `Player HP`
- `Inventory`
- `Location`
- `Mission progress`
- `Remaining time`
- `Base resources`

**Planned outputs**
- `Dynamic quest`
- `Optional event`
- `Risk warning`
- `NPC response`

設計文件沒有提供輸入門檻、決策演算法、Prompt、模型供應商、輸出格式或執行頻率。

### (3) Planned AI Techniques（Source: Project E.X.I.T. Design Document）

| AI Agent 概念 | Project E.X.I.T. 設計文件明示用途 | 狀態 |
|---|---|---|
| **Tool Use** | 取得 Game State、Inventory、Map、Mission 等實際遊戲資料 | Planned |
| **RAG** | 讓 NPC 查詢世界觀、地圖、物品與角色知識 | Planned |
| **Planning** | 用於多步驟複合任務 | Planned |
| **Reflection** | 檢查任務是否可完成、是否與 Game State 衝突、獎勵是否合理 | Planned |
| **Agent Workflow** | 區分 AI Director、Quest Agent、NPC Agent 與 Game System | Planned |

> [!NOTE]
> 文件只說明上述角色與技術的用途，**沒有提供完整的 agent-to-agent 通訊順序、訊息格式或 orchestration state machine**。

### (4) 與已 Ingest 學術概念的邊界（Wiki comparison）
- **RAG**：Project E.X.I.T. 僅規劃以 RAG 讓 NPC 查詢知識；設計文件沒有指定 Lewis et al. (2020) 的 DPR + BART 架構，因此不能視為原始 RAG 論文架構的直接實作。
- **Planning**：文件只寫「用於多步驟複合任務」，沒有指定 PDDL、Fast-Downward 或 LLM+P。
- **Reflection**：文件只定義任務可完成性、Game State 衝突與獎勵合理性的檢查，沒有指定 Reflexion 的 Actor / Evaluator / Self-Reflection、多輪 trial 或 episodic memory。
- **Agent Workflow**：文件明示要區分 AI Director、Quest Agent、NPC Agent 與 Game System，但沒有規定完整的執行順序與角色間通訊協議。

---

## 5. Example
### (1) 外部學術案例：Voyager in Minecraft（Wang et al., 2023）
- **科技樹進展（Table 1）**：木製工具解鎖速度為基線（AutoGPT）的 15.3 倍，石製 8.5 倍，鐵製 6.4 倍，且為全體對比模型中唯一成功解鎖鑽石工具者（1/3 成功，平均 102 次迭代）。
- **探索表現**：收集 63 種獨特道具（3.3 倍於基線），地圖探索距離為基線的 2.3 倍。
- **新世界零樣本泛化（Table 2）**：在 4 項 unseen tasks 中皆達成 3/3 trials per task；其技能庫加入 AutoGPT 後，在部分任務取得原本沒有的成功結果。

### (2) 內部設計案例：Project E.X.I.T. 的來源支援流程
以下不是虛構的一場實際遊戲，而是把設計文件已明示的內容依來源順序整理：

1. 核心遊戲循環：`基地準備 → 接受任務 → 進入地圖 → 探索 / 搜刮 / 戰鬥 → AI 動態事件 → 撤離 → 結算 → 返回基地`。
2. AI Director **預計**讀取 `Player HP`、`Inventory`、`Location`、`Mission progress`、`Remaining time`、`Base resources`。
3. AI Director **預計**決定 `Dynamic quest`、`Optional event`、`Risk warning` 或 `NPC response`。
4. Tool Use、RAG、Planning、Reflection 與 Agent Workflow 皆為 Planned AI Techniques；其用途依上表所列。
5. 角色移動、碰撞、HP 扣除、掉落機率、背包容量等明確規則由一般遊戲程式處理，不交給 LLM 即時決定。

> [!IMPORTANT]
> 設計文件**沒有**提供具體任務內容、HP 門檻、事件範例、撤離點規則、NPC 對話範例或 Agent 之間的完整執行時序，因此本 Wiki 不自行補寫這些內容。

### (3) Voyager vs. Project E.X.I.T.（Wiki source comparison）

| 比較維度 | Voyager in Minecraft（Wang et al., 2023） | Project E.X.I.T.（Internal Design Document） |
|---|---|---|
| **來源性質** | 外部學術研究 | 內部專案設計文件 |
| **環境 / 循環** | Minecraft / MineDojo 開放世界探索 | 設計文件明示基地、任務、地圖探索/搜刮/戰鬥、AI 動態事件、撤離與結算循環 |
| **Agent 結構** | Automatic Curriculum、Skill Library、Iterative Prompting | 區分 AI Director、Quest Agent、NPC Agent 與 Game System；詳細 orchestration 未指定 |
| **外部資料 / 工具** | Mineflayer API 與結構化環境狀態 | Tool Use 規劃取得 Game State、Inventory、Map、Mission |
| **知識 / 記憶** | Skill Library 儲存可執行程式碼技能 | RAG 規劃供 NPC 查詢世界觀、地圖、物品與角色知識；未指定 skill library |
| **反饋 / 檢查** | Environment feedback、execution errors、Self-Verification Critic | Reflection 規劃檢查任務可完成性、Game State 衝突與獎勵合理性；未指定多輪 trial |
| **確定性程式邊界** | 依 Voyager / Mineflayer 研究架構 | 移動、碰撞、HP 扣除、掉落機率、背包容量由一般遊戲程式處理 |
| **驗證狀態** | 有研究實作與實驗結果 | 設計規劃階段；不代表所有功能已完成 |

---

## 6. Limitations / Notes
1. **Project E.X.I.T. 證據效力**：`project-exit-design.md` 只支援 Project E.X.I.T. 的設計描述與 implementation intentions，不能作為一般 AI Agent 效能的研究證據。
2. **實作狀態**：來源只說「以上內容屬於設計規劃，不代表所有功能已完成實作」，因此無法從目前文件判定各模組的個別完成度。
3. **未指定內容**：目前設計文件未提供模型名稱、Prompt、RAG 儲存與檢索實作、Planning 演算法、Reflection 執行流程、Agent 間訊息協議、Tool schema、觸發門檻或效能評估結果。
4. **Voyager 研究邊界**：Voyager 的結果來自 Minecraft / MineDojo 與其研究設定，不應直接作為 Project E.X.I.T. 的實證結果（Wang et al., 2023）。

---

## 7. References
- **Wang et al. (2023)**: Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar, Chaowei Xiao, Yuke Zhu, Linxi Fan, Anima Anandkumar. *VOYAGER: An Open-Ended Embodied Agent with Large Language Models*. arXiv preprint arXiv:2305.16291v2 [cs.AI], 19 Oct 2023. [06-voyager.pdf](../raw/06-voyager.pdf)（後續正式發表於 TMLR, March 2024）。
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024, 18(6): 186345. [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
- **Project E.X.I.T. Design Document**: *Project E.X.I.T. Design Document*. Internal Project Design Document. [project-exit-design.md](../raw/project-exit-design.md)
