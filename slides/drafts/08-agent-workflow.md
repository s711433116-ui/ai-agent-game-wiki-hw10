# Slide 8 — Agent Workflow：把模組串成一個系統

## Main teaching point
Agent Workflow 負責協調 Planning、Tool Use、Retrieval、Action 與 Feedback，讓多個能力形成可持續運作的流程。

## Supporting explanation
教學型閉環可以整理為：

`Plan → Act / Tool → Observe / Evaluate → Re-plan`

不同元件可以掛接在流程中：

- **RAG**：提供外部知識檢索。
- **Tool Use**：執行外部能力。
- **Planning**：安排下一步行動。
- **Reflection**：根據失敗或評估結果修正。
- **Memory**：保存狀態與過去經驗。

不同研究採用的工作流程與回饋機制各有差異；本頁以閉環流程整理其共同的教學結構。

## Example / diagram idea
中央環狀流程：

`Plan → Act → Observe → Evaluate → Re-plan`

外圍掛接：
`Memory / RAG / Tools`

## Source reference
- `slides/cards/07-agent-workflow.md`
- `wiki/agent-workflow.md`
- Wang et al. (2024)
- Schick et al. (2023)
- Lewis et al. (2020)
- Liu et al. (2023)
- Shinn et al. (2023)
- Wang et al. (2023)
