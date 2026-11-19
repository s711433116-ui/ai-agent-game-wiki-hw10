# Evidence Card 08 — 遊戲中的 AI Agent：Voyager

- **Topic**: Game Application
- **Claim / Teaching Point**: VOYAGER 展示 LLM-based embodied agent 如何在 Minecraft 中透過 Automatic Curriculum、Skill Library 與 Iterative Prompting 進行長期自主探索與技能累積。
- **Supporting Source**:
  - `wiki/game-application.md`
  - `wiki/agent-workflow.md`
  - Wang et al. (2023), *VOYAGER*
- **Evidence Summary**:
  - Automatic Curriculum 根據狀態與探索進度提出任務。
  - Skill Library 儲存可執行 JavaScript 技能並檢索重用。
  - Iterative Prompting 利用 environment feedback、execution errors、Self-Verification Critic 修正程式碼。
  - Wiki 記錄：木 / 石 / 鐵工具解鎖速度分別為 AutoGPT baseline 的 15.3x / 8.5x / 6.4x；收集 63 種 unique items（3.3x baseline）；探索距離 2.3x baseline。
- **Allowed Interpretation**: 可把 Voyager 當作「Agent 進入遊戲環境後，如何感知、規劃、執行、得到回饋、累積技能」的研究案例。
- **Limitations**:
  - 實驗來自 Minecraft / MineDojo 與 Mineflayer API。
  - 不能把 Voyager benchmark 轉成 Project E.X.I.T. 的成效證據。
- **Suggested Slide**: Slide 9 — 遊戲中的 AI Agent
