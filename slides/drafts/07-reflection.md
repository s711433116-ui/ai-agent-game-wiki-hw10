# Slide 7 — Reflection：Agent 如何從失敗中修正

## Main teaching point
Reflection 讓 Agent 把失敗經驗轉成可重用的文字反思，下一輪再利用這些經驗修正策略。

## Supporting explanation
Reflexion 的三個核心角色：

- **Actor**：執行任務。
- **Evaluator**：判斷成敗或給出評分。
- **Self-Reflection**：分析失敗並產生文字反思。

基本循環：

`Act → Evaluate → Reflect → Episodic Memory → Retry`

Reflexion 在這個過程中不更新 LLM 權重，而是把文字反思保存在 Episodic Memory 中。

## Example / diagram idea
AlfWorld：

`Trial 1 失敗 → 找出動作順序問題 → 寫入反思 → Trial 2 修正`

## Limitation
Reflection 的效果仍受模型能力與任務特性影響；在需要高度探索的 WebShop 實驗中，Reflexion 仍可能陷入 local minima。

## Source reference
- `slides/cards/06-reflection.md`
- `wiki/reflection.md`
- Shinn et al. (2023), *Reflexion*
