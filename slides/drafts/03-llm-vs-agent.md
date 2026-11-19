# Slide 3 — LLM 與 AI Agent 的差別

## Main teaching point
LLM 著重文字理解與生成；AI Agent 則把模型放進一個可以感知、規劃、行動與接收回饋的系統中。

## Supporting explanation
### LLM
`Input → Generate → Output`

適合：
- 問答
- 摘要
- 文字生成

### AI Agent
`Observe / Memory → Plan → Act / Tool → Feedback → Re-plan`

增加：
- 狀態與記憶
- 任務規劃
- Tool Use
- 環境回饋
- 多輪修正

不同 Agent 系統會依任務需求採用不同流程；本頁以感知、規劃、行動與回饋整理核心差異。

## Example / diagram idea
左右兩欄比較：

左側：單次 LLM 流程  
右側：Agent closed loop

## Source reference
- `slides/cards/02-llm-vs-agent.md`
- `wiki/ai-agent.md`
- Wang et al. (2024)
- Schick et al. (2023)
