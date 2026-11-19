# Evidence Card 06 — Reflection

- **Topic**: Reflection
- **Claim / Teaching Point**: Reflexion 讓 Agent 在失敗後把評估結果轉成自然語言反思並存入 Episodic Memory，下一輪再利用這些反思修正行動；過程不需要更新 LLM 權重。
- **Supporting Source**:
  - `wiki/reflection.md`
  - Shinn et al. (2023), *Reflexion*
- **Evidence Summary**:
  - 三個核心角色：Actor、Evaluator、Self-Reflection。
  - 失敗軌跡經 Evaluator 評估後，由 Self-Reflection 產生自然語言 feedback。
  - 反思文字加入跨 trial episodic memory，供後續 Actor 使用。
  - Reflexion 將此文字回饋描述為 semantic gradient。
- **Allowed Interpretation**: 可用「做錯 → 評估 → 寫下教訓 → 下一輪讀取教訓再試」作為初學者理解。
- **Limitations**:
  - Reflexion 不等於所有形式的 Reflection。
  - starchat-beta 實驗未顯示改善，作者觀察有效 self-correction 可能依賴較強模型。
  - WebShop 實驗顯示 Reflection 仍可能陷入 local minima。
- **Suggested Slide**: Slide 7 — Reflection
