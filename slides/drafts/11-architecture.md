# Slide 11 — 完整架構：AI 與遊戲規則怎麼分工？

## Main teaching point
Project E.X.I.T. 將高階 AI 功能與 deterministic game logic 分工：AI / Agent 處理高階任務與知識功能，Game System 負責明確且可驗證的遊戲規則。

## Supporting explanation
### AI / Agent Layer
- AI Director
- Quest Agent
- NPC Agent
- Tool Use
- RAG
- Planning
- Reflection

### Game System Layer
- Movement
- Collision
- HP deduction
- Drop probability
- Backpack capacity

### 已定義的資料關係
AI Director 預計讀取：
- Player HP
- Inventory
- Location
- Mission progress
- Remaining time
- Base resources

並產生：
- Dynamic quest
- Optional event
- Risk warning
- NPC response

## Example / diagram idea
使用兩層 responsibility map：

**上層：AI / Agent Components**  
AI Director / Quest Agent / NPC Agent  
Tool Use / RAG / Planning / Reflection

**下層：Deterministic Game System**  
Movement / Collision / HP / Drop / Backpack

以資料箭頭表示 AI Director 讀取 Game State；保持元件關係，不建立固定 agent-to-agent 執行時序。

## Limitation
目前來源只定義元件與責任分工；orchestration state machine、訊息格式、Tool schema、Prompt 與觸發門檻仍未指定。

## Source reference
- `slides/cards/10-project-exit-architecture.md`
- `wiki/game-application.md`
- `wiki/agent-workflow.md`
- `raw/project-exit-design.md`

**Source: Project E.X.I.T. Design Document**
