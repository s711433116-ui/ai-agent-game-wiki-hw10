# Slide 9 — 遊戲中的 AI Agent：Voyager

## Main teaching point
VOYAGER 展示 LLM-based Agent 如何在 Minecraft 中自主提出目標、生成可執行技能，並根據環境回饋持續修正。

## Supporting explanation
三個核心組件：

### Automatic Curriculum
依據目前狀態與探索進度，自主提出下一個任務。

### Skill Library
將成功的 JavaScript / Mineflayer 技能存入向量資料庫，以便後續檢索與重用。

### Iterative Prompting
根據 environment feedback、execution errors 與 Self-Verification Critic 反覆修正程式碼。

研究結果包含：

- 木製工具解鎖速度：15.3× AutoGPT baseline
- 石製工具：8.5×
- 鐵製工具：6.4×
- 收集 63 種 unique items：3.3× baseline
- 探索距離：2.3× baseline

## Example / diagram idea
三角圖或循環圖：

`Curriculum ↔ Skill Library ↔ Iterative Prompting`

## Limitation
研究結果來自 Minecraft / MineDojo 的實驗設定，應在相同研究脈絡下解讀。

## Source reference
- `slides/cards/08-voyager-game-agent.md`
- `wiki/game-application.md`
- Wang et al. (2023), *VOYAGER*
