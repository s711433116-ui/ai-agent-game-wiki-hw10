# AGENTS.md

## 1. Project Overview
本專案是一個以「AI Agent 與生成式 AI 遊戲系統」為主題的 LLM Wiki 專案。

主要目標：
1. 以至少 5 篇來源建立可追溯的 LLM Wiki。
2. 使用 Wiki 作為主要知識來源，製作 10–12 頁教學型 HTML 簡報。
3. 以 Graph 與局部 Loop 控制檢索、證據整理、內容生成、忠實度檢查與 HTML 渲染。
4. 保留各階段輸出與檢查紀錄，作為執行結果與反思依據。

主要讀者為沒有 AI Agent 背景、但想了解生成式 AI 如何應用到遊戲系統的學生。

## 2. Scope
Wiki 主題：
1. AI Agent 基本概念
2. Tool Use
3. RAG
4. Planning
5. Reflection
6. Agent Workflow
7. Game Application

Game Application 以 Project E.X.I.T. 作為案例。Project E.X.I.T. 只能作為自建應用案例，不可被寫成外部研究證據。

## 3. Language Rules
- 主要使用台灣繁體中文。
- 技術名詞保留英文，例如 AI Agent、Tool Use、RAG、Planning、Reflection、Agent Workflow、Game State、AI Director。
- 第一次出現的重要術語需附簡短解釋。
- 避免簡體字與中國大陸慣用詞。
- 不把推論寫成來源已證實的事實。

## 4. Source Rules
所有重要概念都必須可追溯到來源。每筆來源至少保留：
- Title
- Author / Organization
- Year
- Original URL or file reference
- Source type
- Covered topics

來源分為：
- External Sources：外部論文、官方文件或可信技術資料。
- Internal Project Source：Project E.X.I.T. 設計文件。

內部設計文件只能支援 Project E.X.I.T. 的設計內容，不能支援一般性的 AI Agent 研究結論。

## 5. Conflict Handling
不同來源若出現定義、方法或結論差異：
1. 並列不同來源的說法。
2. 說明差異可能來自研究情境、方法、資料或定義。
3. 不自行合併成單一結論。
4. 若資料不足以判定，保留不確定性。
5. 禁止用模型常識補成看似一致的答案。

## 6. Project Structure
```text
ai-agent-game-wiki/
├── AGENTS.md
├── requirements-summary.md
├── raw/
│   ├── README.md
│   └── project-exit-design.md
├── wiki/
│   ├── index.md
│   ├── ai-agent.md
│   ├── tool-use.md
│   ├── rag.md
│   ├── planning.md
│   ├── reflection.md
│   ├── agent-workflow.md
│   └── game-application.md
├── slides/
│   ├── sources.md
│   ├── cards/
│   ├── outline.md
│   ├── drafts/
│   ├── deck.html
│   └── report.md
└── log.md
```

## 7. Raw Source Rules
- 所有來源先放入 `/raw/`。
- 外部來源不修改原始 PDF 內容。
- 不把不同來源合併成一份原始檔。
- Project E.X.I.T. 使用 `/raw/project-exit-design.md`。
- Project E.X.I.T. 必須標示為 internal design document。

## 8. Ingest Rules
採小批次 ingest：
- 每批 1–2 篇來源。
- 每批完成後先檢查，再進下一批。
- 每批完成後更新 `log.md`。
- 若使用 Git，每批確認後進行一次 commit。

每批至少檢查：
1. Source metadata 是否正確。
2. 摘要是否忠於原文。
3. Wiki 是否放到正確主題。
4. 是否出現來源沒有支持的內容。
5. 是否遺漏重要限制或條件。

任一項不通過時先修正該批，不繼續下一批。

## 9. Wiki Page Rules
每頁至少包含：
1. Overview
2. Core Concept
3. Why It Matters
4. How It Works
5. Example
6. Limitations / Notes
7. References

規則：
- 重要概念必須有來源支持。
- 不直接複製原文長段落。
- 優先整理來源間的共同點與差異。
- 若來源不足，明確寫出資料不足。
- 不使用 Wiki 外部知識補齊缺口。

## 10. Evidence Card Rules
簡報生成前必須建立 Evidence Cards，放在 `/slides/cards/`。

每張卡只能包含：
- Topic
- Claim / Teaching Point
- Supporting Source
- Evidence Summary
- Allowed Interpretation
- Limitations
- Suggested Slide

規則：
- 卡片內容必須來自 Wiki 或原始來源。
- Project E.X.I.T. 內容標示為 internal project source。
- 一張卡只處理一個主要 teaching point。

## 11. Coverage Check
進入 Outline 前，確認下列主題都有足夠證據：
- AI Agent
- Tool Use
- RAG
- Planning
- Reflection
- Agent Workflow
- Game Application

若某主題沒有來源、來源不足或只有模糊敘述，Coverage Check = Fail。
Fail 時先回 Wiki Retrieval；若 Wiki 本身不足，再回 Ingest。禁止直接進 Outline。

## 12. Slide Deck Goal
最終產出 10–12 頁教學型 HTML 簡報，面向 AI Agent 初學者。

暫定題目：
**AI Agent 如何成為遊戲系統的一部分？——以 Project E.X.I.T. 為例**

暫定結構：
1. Title
2. AI Agent 是什麼？
3. LLM 與 AI Agent 的差別
4. Tool Use
5. RAG
6. Planning
7. Reflection
8. Agent Workflow
9. 遊戲中的 AI Agent
10. Project E.X.I.T. 案例
11. 完整架構
12. Key Takeaways

## 13. Outline Rules
Outline 存於 `/slides/outline.md`，只能使用 Wiki 與 Evidence Cards。

Outline 完成後必須進行 Human Outline Check，至少確認：
- 教學順序是否合理。
- 是否遺漏主要主題。
- 是否先解釋概念再進入案例。
- Project E.X.I.T. 比例是否過高。
- 每張投影片是否只有一個主要 teaching point。

Human Check 通過後才能進 Slide Draft。

## 14. Slide Draft Rules
逐頁稿放在 `/slides/drafts/`。

每張內容頁必須包含：
- Slide title
- Main teaching point
- Supporting explanation
- Example / diagram idea
- Source reference

除封面與純導覽 / 總結頁外，每張內容頁都必須可追溯到 Wiki 或原始來源。
Project E.X.I.T. 頁面標示：
`Source: Project E.X.I.T. Design Document`

不得在排版階段新增新的研究內容。

## 15. Fidelity Check
Slide Draft 完成後進行 Fidelity Check。

AI Reviewer 必須獨立審查，只提供：
- Slide Draft / deck
- Evidence Cards
- Sources

不提供原生成對話。

檢查：
1. 是否忠於來源。
2. 是否把結論講反。
3. 是否有 unsupported claim。
4. 是否有來源但引用錯誤。
5. 是否混淆外部研究與 Project E.X.I.T. 設計。

若 Fail：
- 只修改被指出的頁面。
- 已通過頁面不動。
- 最多修正 3 輪。

## 16. HTML Render Rules
HTML 最終輸出：`/slides/deck.html`

渲染時：
- 只能使用已通過 Fidelity Check 的逐頁稿。
- 不得在渲染階段改寫內容。
- 可調整版面、字級、留白、圖表位置。
- 不得新增新的事實或案例。

## 17. Programmatic Check
HTML 至少檢查：
- 檔案可正常開啟。
- 10–12 頁投影片能正常切換。
- 無 broken internal links。
- 引用連結存在。
- 每張內容頁有 source reference。
- 沒有明顯文字 overflow。
- 沒有主要元素超出 viewport。
- 標題與內容區塊完整。

若 Fail：
- 版面問題退回 Render。
- 引用缺漏退回 Slide Draft。
- 來源不足退回 Evidence / Retrieval。

## 18. Loop Rules
### Evidence Card Loop
Evidence Card → Check → Fix，最多 2 輪。

### Slide Fidelity Loop
Slide Draft → Fidelity Check → Fix，最多 3 輪。

### Render Loop
HTML Render → Render Check → Layout Fix，最多 2 輪。

每輪只修檢查報告指出的問題。

## 19. Stop Conditions
符合任一條件時停止目前 Loop：
1. 所有檢查通過。
2. 已達該 Loop 最大修正輪數。
3. Wiki / source 資料不足。

資料不足時不得硬湊，需在 report 中記錄，必要時回 Retrieval / Ingest。

## 20. Graph
```text
Raw Sources
    ↓
Small-batch Ingest
    ↓
LLM Wiki
    ↓
Wiki Retrieval
    ↓
Coverage Check
   ↙         ↘
 Fail        Pass
  ↓            ↓
Retrieval /  Evidence Cards
Ingest          ↓
  ↑          Outline
  └──────   Human Check
                ↓
           Slide Draft
                ↓
          Fidelity Check
           ↙         ↘
         Fail        Pass
          ↓            ↓
     Fix affected   HTML Render
       slides           ↓
                    Render Check
                    ↙         ↘
                  Fail        Pass
                   ↓            ↓
              Layout Fix     Final Deck
```

## 21. Logging Rules
所有重要步驟記錄在 `/log.md`。

每次記錄：
- Date / Run ID
- Input / Topic
- Sources processed
- Node executed
- Check result
- Pass / Fail
- Error reason
- Fix action
- Loop count
- Output file

禁止事後虛構執行紀錄。

## 22. Final Report
`/slides/report.md` 至少包含：
1. Wiki 主題與簡介
2. Ingest 資料清單
3. Graph / Loop 說明
4. 一次實際執行紀錄
5. 哪個節點最常出錯
6. 問題在哪一個檢查節點被發現
7. 是否把檢查往前移
8. Workflow 是否修改
9. 修改原因
10. Final result

## 23. Definition of Done
- [ ] 至少 ingest 5 篇來源。
- [ ] Wiki 七個主題都有內容。
- [ ] 重要內容可追溯來源。
- [ ] Project E.X.I.T. 與研究來源清楚區分。
- [ ] Evidence Cards 完成。
- [ ] Outline 經 Human Check。
- [ ] HTML 簡報完成。
- [ ] Fidelity Check 通過或已記錄最大輪數結果。
- [ ] Render Check 通過。
- [ ] 至少保留一次真實執行紀錄。
- [ ] report.md 完成。
- [ ] 反思根據實際執行結果撰寫。
