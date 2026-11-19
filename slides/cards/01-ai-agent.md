# Evidence Card 01 — AI Agent 基本概念

- **Topic**: AI Agent
- **Claim / Teaching Point**: LLM-based Autonomous Agent 不只是一次性的文字生成器；Wang et al. (2024) 的綜述以 Profile、Memory、Planning、Action 四個模組整理 Agent 架構，讓系統能以角色設定、記憶、規劃與行動和環境持續互動。
- **Supporting Source**:
  - `wiki/ai-agent.md` — Unified Architecture Framework
  - Wang et al. (2024), *A Survey on Large Language Model based Autonomous Agents*
- **Evidence Summary**:
  - Profile：定義角色、性格與社會背景。
  - Memory：儲存環境資訊與自身歷程。
  - Planning：將複雜任務拆成子任務，並可納入回饋。
  - Action：將高階決策轉成具體輸出並與環境互動。
- **Allowed Interpretation**: 可用「LLM 作為核心控制器，加上 Profile / Memory / Planning / Action」向初學者解釋 LLM-based Agent 的模組化概念。
- **Limitations**: 四模組是 Wang et al. (2024) 對既有研究的統整框架，不是所有 Agent 系統唯一必須遵循的標準架構。
- **Suggested Slide**: Slide 2 — AI Agent 是什麼？
