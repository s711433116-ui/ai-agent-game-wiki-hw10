# Slide 10 — Project E.X.I.T.：把 Agent 概念放進撤離遊戲

## Main teaching point
Project E.X.I.T. 用 AI Director 與多個 Agent 概念，規劃在撤離遊戲循環中加入動態任務、事件與知識查詢能力。

## Supporting explanation
### Core Loop
`基地準備`
→ `接受任務`
→ `進入地圖`
→ `探索 / 搜刮 / 戰鬥`
→ `AI 動態事件`
→ `撤離`
→ `結算`
→ `返回基地`

### AI Director — Planned Inputs
- Player HP
- Inventory
- Location
- Mission progress
- Remaining time
- Base resources

### AI Director — Planned Outputs
- Dynamic quest
- Optional event
- Risk warning
- NPC response

### Planned AI Techniques
Tool Use、RAG、Planning、Reflection、Agent Workflow

## Example / diagram idea
上半部畫 Core Loop；下半部畫 AI Director 的 Inputs / Outputs。

## Limitation
目前處於 Design-stage / Planned；個別功能的完成度尚未在設計文件中逐項說明。

## Source reference
- `slides/cards/09-project-exit.md`
- `wiki/game-application.md`
- `raw/project-exit-design.md`

**Source: Project E.X.I.T. Design Document**
