# Evidence Card 05 — Planning

- **Topic**: Planning
- **Claim / Teaching Point**: Planning 的核心是把 Long-horizon Goal 分解成具因果相依性的子任務或動作序列；LLM+P 是一個以 LLM 做自然語言轉譯、外部 Symbolic Planner 做嚴格求解的代表性方法。
- **Supporting Source**:
  - `wiki/planning.md`
  - Liu et al. (2023), *LLM+P*
  - Wang et al. (2024) Survey
- **Evidence Summary**:
  - LLM+P：自然語言 → Problem PDDL → Fast-Downward → symbolic plan → 自然語言 / 執行動作。
  - 研究區分 LLM 的 linguistic competence 與 functional planning competence。
  - 在多個 IPC-derived domains 中，LLM+P 的可行 / 最優計畫成功率高於直接 LLM-as-Planner baseline；但 Floortile 也展示前端 PDDL 轉譯錯誤會讓整個管線失敗。
- **Allowed Interpretation**: 可用「LLM 擅長語言轉譯，外部規劃器負責嚴格搜尋」說明一種 Planning 分工方式。
- **Limitations**:
  - LLM+P 不是所有 Agent Planning 的唯一架構。
  - 成功依賴正確 Domain PDDL、Problem PDDL 與 planner 設定。
  - Project E.X.I.T. 沒有指定採用 PDDL / Fast-Downward。
- **Suggested Slide**: Slide 6 — Planning
