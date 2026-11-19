# Slide Outline

## Deck Title
**AI Agent 如何成為遊戲系統的一部分？——以 Project E.X.I.T. 為例**

## Audience
沒有 AI Agent 背景，但想了解生成式 AI 如何應用到遊戲系統的學生。

## Teaching Flow
先建立 AI Agent 基礎概念，再依序介紹 Tool Use、RAG、Planning、Reflection 與 Agent Workflow，接著用 Voyager 作為外部研究案例，最後映射到 Project E.X.I.T. 的內部設計案例。

---

## Slide 1 — Title
### Title
AI Agent 如何成為遊戲系統的一部分？

### Subtitle
以 Project E.X.I.T. 為例

### Main Teaching Point
建立本次簡報主題與「LLM → Agent → Game System」的整體脈絡。

### Visual Idea
簡潔的「LLM → Agent → Game System」概念圖。

### Source
Cover slide，不要求內容來源。

---

## Slide 2 — AI Agent 是什麼？
### Main Teaching Point
LLM-based Agent 不只是一次性的文字生成器，而是由多個功能模組共同支援持續感知、規劃與行動。

### Supporting Explanation
以 Wang et al. (2024) 綜述的四模組架構介紹：
- Profile
- Memory
- Planning
- Action

### Example / Diagram Idea
四個模組環繞中央 LLM Controller：
`Profile → Memory → Planning → Action`

### Source
- Evidence Card 01
- `wiki/ai-agent.md`
- Wang et al. (2024)

---

## Slide 3 — LLM 與 AI Agent 的差別
### Main Teaching Point
LLM 主要負責生成與回答；Agent 則加入狀態、記憶、規劃、行動與回饋，使系統可以處理多步驟任務。

### Supporting Explanation
教學對照：
- LLM：Input → Generate → Output
- AI Agent：Observe / Memory → Plan → Act / Tool → Feedback → Re-plan

### Example / Diagram Idea
左右雙欄比較：
- 左：單次 LLM 問答
- 右：Agent closed loop

### Source
- Evidence Card 02
- `wiki/ai-agent.md`
- Wang et al. (2024)
- Schick et al. (2023)

### Limitation Note
不同 Agent 系統會依任務需求採用不同流程；本頁以感知、規劃、行動與回饋整理核心差異。

---

## Slide 4 — Tool Use：讓 Agent 能使用外部能力
### Main Teaching Point
Tool Use 擴展模型的 Action Space，讓 Agent 在需要時呼叫 API、資料庫或外部專家模組。

### Supporting Explanation
以 Toolformer 說明：
1. 模型決定是否需要工具
2. 產生 API call
3. 外部工具執行
4. 將結果放回生成上下文

典型工具：
Calculator、QA、Wikipedia Search、Machine Translation、Calendar。

### Example / Diagram Idea
`LLM → API Call → Tool → Result → LLM`

### Source
- Evidence Card 03
- `wiki/tool-use.md`
- Schick et al. (2023)

### Limitation Note
Toolformer 原始研究仍有 Tool Chaining 與互動式多輪查詢限制。

---

## Slide 5 — RAG：讓 Agent 查外部知識
### Main Teaching Point
RAG 的核心概念是「先檢索外部知識，再生成」，讓模型不必只依賴參數中的既有知識。

### Supporting Explanation
介紹：
- Parametric Memory
- Non-parametric Memory
- Retriever
- Generator

原始 RAG：
`Query → DPR Retrieval → Retrieved Documents → BART Generation`

### Example / Diagram Idea
畫出「問題 → 外部知識庫 → 相關文件 → LLM → 回答」。

### Source
- Evidence Card 04
- `wiki/rag.md`
- Lewis et al. (2020)

### Limitation Note
Lewis et al. (2020) 的原始 RAG 採 DPR + BART 端到端架構；現代常見的 Vector DB + Prompt 實作則屬後續延伸形式。

---

## Slide 6 — Planning：把長程目標拆成可執行步驟
### Main Teaching Point
Planning 的重點是把 Long-horizon Goal 拆成具前後依賴與可行性的子任務。

### Supporting Explanation
以 LLM+P 作代表：
`Natural Language → Problem PDDL → Symbolic Planner → Plan → Natural Language / Actions`

說明分工：
- LLM：語言理解與格式轉譯
- Planner：嚴格狀態搜尋

### Example / Diagram Idea
用「整理房間」或論文中的 tidy-up task 畫出多步驟規劃鏈。

### Source
- Evidence Card 05
- `wiki/planning.md`
- Liu et al. (2023)

### Limitation Note
Agent Planning 可採用多種方法；LLM+P 以 PDDL 與 Symbolic Planner 為其中一種代表性做法。Project E.X.I.T. 目前僅規劃多步驟複合任務，尚未指定 PDDL 或 Fast-Downward。

---

## Slide 7 — Reflection：Agent 如何從失敗中修正
### Main Teaching Point
Reflection 讓 Agent 把失敗經驗轉成可重用的文字反思，下一輪再利用這些經驗修正策略。

### Supporting Explanation
Reflexion 三角色：
- Actor
- Evaluator
- Self-Reflection

基本循環：
`Act → Evaluate → Reflect → Store in Episodic Memory → Retry`

### Example / Diagram Idea
以 AlfWorld 案例簡化：
Trial 1 失敗 → 產生反思 → Trial 2 修正成功。

### Source
- Evidence Card 06
- `wiki/reflection.md`
- Shinn et al. (2023)

### Limitation Note
Reflection 不一定保證成功；WebShop 等高探索任務中仍可能陷入 local minima。

---

## Slide 8 — Agent Workflow：把模組串成一個系統
### Main Teaching Point
真正的 Agent 系統需要 Workflow 協調 Planning、Tool Use、Retrieval、Action 與 Feedback。

### Supporting Explanation
教學型閉環示意：
`Plan → Act / Tool → Observe / Evaluate → Re-plan`

並說明：
- RAG 可作為 Retrieval 子流程
- LLM+P 可作為 Planning 子流程
- Reflexion / Voyager 顯示 Feedback / Retry 機制

### Example / Diagram Idea
中央環狀流程圖，旁邊掛接 RAG、Tools、Memory。

### Source
- Evidence Card 07
- `wiki/agent-workflow.md`

### Limitation Note
不同研究採用的工作流程與回饋機制各有差異；本頁以閉環流程整理其共同的教學結構。

---

## Slide 9 — 遊戲中的 AI Agent：Voyager
### Main Teaching Point
Voyager 是一個實際研究案例，展示 LLM-based Agent 如何在 Minecraft 中自主探索、累積技能並根據環境回饋修正。

### Supporting Explanation
三大組件：
- Automatic Curriculum
- Skill Library
- Iterative Prompting Mechanism

可選擇性呈現研究結果：
- 木製工具：15.3× AutoGPT baseline
- 石製工具：8.5×
- 鐵製工具：6.4×
- 63 unique items：3.3× baseline
- 探索距離：2.3×

### Example / Diagram Idea
三模組三角圖：
Curriculum ↔ Skill Library ↔ Iterative Prompting

### Source
- Evidence Card 08
- `wiki/game-application.md`
- Wang et al. (2023)

### Limitation Note
研究結果來自 Minecraft / MineDojo 的實驗設定，應在相同研究脈絡下解讀。

---

## Slide 10 — Project E.X.I.T.：把 Agent 概念放進撤離遊戲
### Main Teaching Point
Project E.X.I.T. 是 Internal Design Case，用來展示如何把 Tool Use、RAG、Planning、Reflection 與 Agent Workflow 映射到遊戲系統設計。

### Supporting Explanation
Core Loop：
`基地準備 → 接受任務 → 進入地圖 → 探索 / 搜刮 / 戰鬥 → AI 動態事件 → 撤離 → 結算 → 返回基地`

AI Director planned inputs：
- Player HP
- Inventory
- Location
- Mission progress
- Remaining time
- Base resources

Planned outputs：
- Dynamic quest
- Optional event
- Risk warning
- NPC response

### Example / Diagram Idea
Core Loop 橫向流程圖，下方放 AI Director inputs / outputs。

### Source
- Evidence Card 09
- `wiki/game-application.md`
- `raw/project-exit-design.md`

### Source Label
**Source: Project E.X.I.T. Design Document**

### Limitation Note
目前處於 Design-stage / Planned；個別功能的完成度尚未在設計文件中逐項說明。

---

## Slide 11 — 完整架構：AI 與遊戲規則怎麼分工？
### Main Teaching Point
Project E.X.I.T. 將高階 AI 功能與 deterministic game logic 分工：AI / Agent 處理高階任務與知識功能，Game System 負責明確且可驗證的遊戲規則。

### Supporting Explanation
#### AI / Agent Layer
- AI Director
- Quest Agent
- NPC Agent
- Tool Use
- RAG
- Planning
- Reflection

#### Game System Layer
- Movement
- Collision
- HP deduction
- Drop probability
- Backpack capacity

### Example / Diagram Idea
使用「兩層元件與責任邊界圖」：
上層：AI / Agent Components  
下層：Deterministic Game System

可畫資料來源與責任關係；agent-to-agent 的固定執行順序目前尚未由來源定義。

### Source
- Evidence Card 10
- `wiki/game-application.md`
- `wiki/agent-workflow.md`
- `raw/project-exit-design.md`

### Source Label
**Source: Project E.X.I.T. Design Document**

### Limitation Note
目前來源只定義元件與責任分工；orchestration state machine、訊息格式、Tool schema、Prompt 與觸發門檻仍未指定。

---

## Slide 12 — Key Takeaways
### Main Teaching Point
AI Agent 的價值來自「把 LLM 放進一個可感知、可規劃、可使用工具、可取得知識、可接受回饋的系統」。

### Supporting Explanation
三個結論：
1. **Agent ≠ 只多一個 Prompt**：需要 Memory / Planning / Action / Feedback 等系統設計。
2. **外部能力很重要**：Tool Use、RAG、Planning、Reflection 各自處理不同問題。
3. **遊戲設計需要責任邊界**：Project E.X.I.T. 將高階 AI 功能與 deterministic game rules 分開。

### Example / Diagram Idea
回顧式整合圖：
`LLM → Agent Components → Game System`

### Source
Summary slide；只摘要 Slides 2–11 已通過之 Evidence Cards，不新增新事實。

---

# Human Outline Check

**Status: PASS — User approved on 2026-11-19**

請確認以下項目：

- [x] 教學順序是否合理：AI Agent → 元件 → Workflow → Game Case
- [x] 是否遺漏主要主題
- [x] 是否先解釋概念再進 Project E.X.I.T.
- [x] Project E.X.I.T. 比例是否適中
- [x] 每張投影片是否只有一個主要 teaching point
- [x] Slide 9 Voyager 是否保留為研究案例
- [x] Slide 10–11 是否清楚標示 Project E.X.I.T. 為 Internal Design Case
- [x] Slide 11 是否避免虛構完整 orchestration flow

Human Outline Check 已通過，進入 Slide Draft。
