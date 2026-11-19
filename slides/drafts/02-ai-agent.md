# Slide 2 — AI Agent 是什麼？

## Main teaching point
AI Agent 讓 LLM 從單次文字生成，進一步成為能記住資訊、規劃任務並對環境採取行動的系統。

## Supporting explanation
Wang et al. (2024) 以四個核心模組整理 LLM-based Agent：

- **Profile**：定義角色、個性與任務定位。
- **Memory**：保存環境資訊與過去經驗。
- **Planning**：把複雜目標拆成可執行的子任務。
- **Action**：把高階決策轉成實際行動或工具呼叫。

這四個模組共同讓 Agent 能持續和環境互動，而不是只完成一次回答。

## Example / diagram idea
以中央 LLM Controller 搭配四個模組：

`Profile / Memory / Planning / Action`

用環狀或四象限圖呈現。

## Source reference
- `slides/cards/01-ai-agent.md`
- `wiki/ai-agent.md`
- Wang et al. (2024), *A Survey on Large Language Model based Autonomous Agents*
