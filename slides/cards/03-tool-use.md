# Evidence Card 03 — Tool Use

- **Topic**: Tool Use
- **Claim / Teaching Point**: Tool Use 讓語言模型 / Agent 透過標準介面調用外部 API、資料庫或專家模組，擴展原本只能依賴模型內部知識的 Action Space。
- **Supporting Source**:
  - `wiki/tool-use.md`
  - Schick et al. (2023), *Toolformer*
  - Wang et al. (2024) Survey
- **Evidence Summary**:
  - Toolformer 以少量 API 示範，自監督學習「何時呼叫、呼叫什麼、輸入什麼，以及如何利用回傳結果」。
  - 推論時模型生成 API call，取得外部結果後再恢復文字解碼。
  - Toolformer 實驗涵蓋 Calculator、QA、Wikipedia Search、Machine Translation、Calendar。
- **Allowed Interpretation**: 對初學者可用「Agent 不必把所有能力都塞在模型裡；需要時可以呼叫工具」說明 Tool Use。
- **Limitations**:
  - Toolformer 無法自然完成 Tool Chaining。
  - 原始研究缺乏互動式多輪查詢。
  - 實驗中的「每個輸入最多一次 API call」是防止 API loop 的評測限制，不是架構本體的必然限制。
- **Suggested Slide**: Slide 4 — Tool Use
