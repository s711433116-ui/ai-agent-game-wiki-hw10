# Slide 12 — Key Takeaways

## Main teaching point
AI Agent 的價值來自把 LLM 放進一個能取得資訊、規劃任務、採取行動並接收回饋的系統。

## Supporting explanation
### 1. Agent = LLM + System
Memory、Planning、Action 與 Feedback 讓模型可以處理持續性的多步驟任務。

### 2. 不同元件解決不同問題
- Tool Use：連接外部能力
- RAG：取得外部知識
- Planning：拆解長程目標
- Reflection：利用失敗經驗修正

### 3. 遊戲系統需要清楚分工
Project E.X.I.T. 將高階 AI 功能與明確遊戲規則分開：
Agent 處理任務、知識與高階輸出；Game System 保留移動、碰撞、HP、掉落與背包容量等 deterministic rules。

## Example / diagram idea
最終整合圖：

`LLM → Agent Components → Game System`

下方小字：
`Tool Use / RAG / Planning / Reflection / Feedback`

## Source reference
Summary slide；僅摘要 Slides 2–11 已通過之內容，不新增新事實。
