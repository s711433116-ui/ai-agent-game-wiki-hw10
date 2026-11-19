# Evidence Card 02 — LLM 與 AI Agent 的差別

- **Topic**: AI Agent
- **Claim / Teaching Point**: 單純 LLM 主要以被動問答或文字生成運作；Agent 架構則加入記憶、規劃、行動與環境回饋，使模型能在多步驟任務中持續調整行為。
- **Supporting Source**:
  - `wiki/ai-agent.md` — Why It Matters / How It Works
  - Wang et al. (2024)
  - Schick et al. (2023) 作為 Tool Use 的直接研究例子
- **Evidence Summary**:
  - Wiki 指出自主代理需要自主感知、角色設定、主動規劃與動態環境互動。
  - Planning 的分步計畫可指導 Action；Action 結果再回到 Memory 或 Planning 進行 Re-planning。
  - Tool Use 可進一步讓模型透過外部 API / 程式取得模型本身無法直接可靠完成的資訊或能力。
- **Allowed Interpretation**: 可做簡化對照：「LLM = 生成 / 回答；Agent = LLM + 狀態 / 記憶 + 規劃 + 行動 + 回饋」。
- **Limitations**: 這是教學層級的比較性整理；不應聲稱所有 LLM 都只能被動問答，也不應把所有 Agent 實作都固定成同一套流程。
- **Suggested Slide**: Slide 3 — LLM 與 AI Agent 的差別
