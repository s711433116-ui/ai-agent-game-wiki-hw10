# Slide 4 — Tool Use：讓 Agent 能使用外部能力

## Main teaching point
Tool Use 讓 Agent 在需要時呼叫外部 API、資料庫或專家模組，把模型的 Action Space 延伸到文字生成以外。

## Supporting explanation
以 Toolformer 為例，基本流程可整理為：

1. 模型判斷是否需要工具。
2. 產生 API call。
3. 外部工具執行並回傳結果。
4. 結果插回生成上下文。
5. 模型繼續生成後續內容。

Toolformer 實驗整合的工具包含：

- Calculator
- Question Answering
- Wikipedia Search
- Machine Translation
- Calendar

## Example / diagram idea
`LLM → API Call → Tool → Result → LLM`

下方補一個 Calculator 或 WikiSearch 的簡短示意。

## Limitation
Toolformer 原始研究仍存在 Tool Chaining 與互動式多輪查詢限制。

## Source reference
- `slides/cards/03-tool-use.md`
- `wiki/tool-use.md`
- Schick et al. (2023), *Toolformer*
