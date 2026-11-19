# Slide 6 — Planning：把長程目標拆成可執行步驟

## Main teaching point
Planning 讓 Agent 將 Long-horizon Goal 拆成具前後關係與可行性的子任務。

## Supporting explanation
Liu et al. (2023) 的 LLM+P 展示一種 Planning 分工：

`Natural Language`
→ `Problem PDDL`
→ `Symbolic Planner`
→ `Plan`
→ `Natural Language / Actions`

其中：

- **LLM**：負責自然語言理解與格式轉譯。
- **Symbolic Planner**：負責嚴格的狀態搜尋與前置條件檢查。

研究也顯示，前端 PDDL 轉譯若遺漏關鍵條件，整個規劃流程仍可能失敗。

## Example / diagram idea
用論文中的 tidy-up task 畫出多步驟規劃鏈。

## Limitation
Agent Planning 可採用多種方法；LLM+P 以 PDDL 與 Symbolic Planner 為其中一種代表性做法。Project E.X.I.T. 目前僅規劃多步驟複合任務，尚未指定 PDDL 或 Fast-Downward。

## Source reference
- `slides/cards/05-planning.md`
- `wiki/planning.md`
- Liu et al. (2023), *LLM+P*
