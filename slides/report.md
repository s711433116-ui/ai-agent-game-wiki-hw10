# Final Project Report

## 1. Wiki 主題與簡介

本專案主題為：

**AI Agent 與生成式 AI 遊戲系統**

目標是建立一套可追溯來源的 LLM Wiki，讓沒有 AI Agent 背景的學生理解生成式 AI 如何從單純的文字生成模型，逐步延伸成具備 Tool Use、RAG、Planning、Reflection 與 Agent Workflow 的系統，最後再以 **Project E.X.I.T.** 作為內部應用案例，說明這些概念如何映射到遊戲設計。

Wiki 共包含七個主題：

1. AI Agent 基本概念
2. Tool Use
3. RAG
4. Planning
5. Reflection
6. Agent Workflow
7. Game Application

最終簡報題目為：

**AI Agent 如何成為遊戲系統的一部分？——以 Project E.X.I.T. 為例**

---

## 2. Ingest 資料清單

本專案共處理 **6 篇外部學術來源 + 1 份內部專案設計文件**。

### External Academic Sources

1. **Wang et al. (2024)**  
   *A Survey on Large Language Model based Autonomous Agents*  
   用於 AI Agent 基本架構、Memory、Planning、Action、Workflow 與相關研究分類。

2. **Schick et al. (2023)**  
   *Toolformer: Language Models Can Teach Themselves to Use Tools*  
   用於 Tool Use、自監督 API 呼叫學習與工具限制。

3. **Lewis et al. (2020)**  
   *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*  
   用於 RAG、Parametric / Non-parametric Memory、DPR + BART 與知識索引更新。

4. **Liu et al. (2023)**  
   *LLM+P: Empowering Large Language Models with Optimal Planning Proficiency*  
   用於 Planning、PDDL、Symbolic Planner 與長程規劃。

5. **Shinn et al. (2023)**  
   *Reflexion: Language Agents with Verbal Reinforcement Learning*  
   用於 Reflection、Actor / Evaluator / Self-Reflection 與 Episodic Memory。

6. **Wang et al. (2023)**  
   *VOYAGER: An Open-Ended Embodied Agent with Large Language Models*  
   用於遊戲中的 LLM-based Agent、Automatic Curriculum、Skill Library 與 Iterative Prompting。

### Internal Project Source

7. **Project E.X.I.T. Design Document**  
   用於 Project E.X.I.T. 的 Core Loop、AI Director、Planned AI Techniques 與 deterministic game logic boundary。

此來源只支援 Project E.X.I.T. 的設計規劃與 implementation intentions，不作為一般 AI Agent 成效的外部學術證據。

---

## 3. Graph / Loop 設計

本專案採用以下主要 Graph：

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
  ↑           Outline
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

### Loop 設計

- **Evidence Card Loop**：最多 2 輪
- **Slide Fidelity Loop**：最多 3 輪
- **Render Loop**：最多 2 輪

每一輪只修正檢查報告指出的問題。

### Stop Conditions

任一條件成立即停止當前 Loop：

1. 所有檢查通過
2. 達到最大修正輪數
3. Wiki / source 資料不足

若來源不足，回到 Retrieval 或 Ingest，不使用模型常識硬補。

---

## 4. 實際執行紀錄

### Batch 1 — AI Agent + Toolformer

第一批處理 AI Agent Survey 與 Toolformer。

主要修正包含：

- Toolformer 在不同 benchmark 上的比較結果需分開描述。
- LAMA / math 任務可超越更大型模型，但 WebQuestions / Natural Questions 等 open-domain QA 整體仍低於 GPT-3 175B。
- Perplexity 結論改為「相較 matched LM fine-tune 沒有額外惡化」，避免過度延伸。
- Survey 中多代理可靠性相關敘述改為保守表述。

完成後 commit：

`f662d7f complete batch 1 ingest and fidelity cleanup`

---

### Batch 2 — RAG + Planning

第二批處理 RAG 與 LLM+P。

實際發現兩個重要問題：

1. **Shared registry regression**  
   Batch 2 更新來源 registry 時曾意外改動已完成的 Batch 1 metadata，因此後續增加 prior-batch regression check。

2. **Appendix coverage error**  
   初次審查只看 RAG 主文，曾錯誤判定：
   - 21M 728-dimensional vectors
   - Retrieval Collapse  
   缺乏來源支持。

   重新檢查完整版本 Appendix E / F 後確認兩者皆有來源，因此恢復並標示 appendix / preliminary observation 邊界。

其他修正：

- RAG hallucination wording 改為作者觀察到相較 BART 較少 hallucination，避免加入未支持的「顯著」。
- 626M 參數拆解為 BART + DPR encoders，並說明 fine-tuning 時 document encoder / index 固定。
- LLM+P 結論限制於受測 planning domains。
- 移除未有來源的「60 FPS」描述。

完成後 commit：

`5a4ac1f complete batch 2 rag and planning ingest`

---

### Batch 3 — Reflexion + Voyager

第三批處理 Reflexion 與 Voyager。

主要修正：

- Reflexion arXiv 版本與正式 NeurIPS 作者名單差異分開記錄。
- Voyager arXiv 2023 與正式 TMLR 2024 publication 分開記錄。
- 曾將 Voyager 中兩個不同位置的案例內容錯誤組合成一個事件，後續移除。
- HotPotQA 的 20 percentage points 與 14 percentage points 分開對應不同 evaluation context。
- Voyager zero-shot generalization 限定為 4 個 unseen tasks、各 3/3 trials。
- 將 catastrophic forgetting 描述修正為 alleviates。
- ReAct、DEPS、SayPlan 等未直接 Ingest 原始論文的方法，明確標示為 Survey secondary citations。

這一批最重要的經驗是：

**即使兩個細節都真的存在於同一篇論文，模型仍可能把它們錯誤拼接成一個不存在的 composite case。**

因此後續 Evidence Card 更重視 source location 與案例邊界。

完成後 commit：

`29e64c5 complete batch 3 reflection and voyager ingest`

---

### Batch 4 — Project E.X.I.T.

第四批處理 internal design document。

初次輸出出現明顯 over-expansion，例如自行增加：

- 具體 HP 數值門檻
- 具體 Dynamic Event
- 撤離點規則
- NPC 廣播內容
- 高風險高回報設定
- Agent 間固定執行順序
- 未定義的 runtime / performance 假設

重新回到 `project-exit-design.md` 後，只保留文件真正明示的：

- Core Loop
- AI Director 6 項 planned inputs
- 4 項 planned outputs
- Tool Use / RAG / Planning / Reflection / Agent Workflow 的用途
- deterministic game rules boundary
- Design-stage / Planned 狀態

並明確記錄目前沒有定義完整 agent-to-agent orchestration。

完成後 commit：

`532a5cf complete batch 4 project exit internal source ingest`

---

## 5. Wiki Retrieval → Evidence Cards → Outline

完成全部 Ingest 後進行 Coverage Check。

七個 Wiki 主題皆具備足夠來源，因此：

**Coverage Check = PASS**

接著建立 Evidence Cards，每張卡只處理一個主要 teaching point，並將 Project E.X.I.T. 標示為 internal source。

12 頁簡報結構確立為：

1. Title
2. AI Agent 是什麼？
3. LLM 與 AI Agent 的差別
4. Tool Use
5. RAG
6. Planning
7. Reflection
8. Agent Workflow
9. 遊戲中的 AI Agent：Voyager
10. Project E.X.I.T.
11. AI / Agent 與 Game System 責任邊界
12. Key Takeaways

Human Outline Check 後，將部分較防禦式的句型改為直接教學敘述，例如：

- 直接說明 Agent Planning 可採多種方法
- 直接說明 Project E.X.I.T. 尚未指定 PDDL / Fast-Downward
- 直接說明不同研究採用不同 Workflow

避免投影片持續使用「這不是……」「不代表……」等句型。

---

## 6. Slide Fidelity Check

Slide Draft 完成後進行 Independent Fidelity Check。

審查輸入只包含：

- Slide Draft
- Evidence Cards
- Sources

檢查項目：

- unsupported claim
- conclusion reversal
- citation mismatch
- numeric mismatch
- external research / internal design 混淆

結果：

**PASS**

特別核對 Voyager：

- Wood tools: 15.3× baseline
- Stone tools: 8.5×
- Iron tools: 6.4×
- 63 unique items / 3.3× baseline
- Exploration distance: 2.3×

均與 Evidence Card 一致。

Project E.X.I.T. Slides 10–11 亦維持：

- Internal Project Design Document
- Design-stage / Planned
- 無固定 orchestration sequence
- 無未定義 model / Prompt / Tool schema / threshold

---

## 7. HTML Render / Render Check

HTML Deck 共 **12 頁**。

HTML Render 階段未新增新的研究內容，只進行版面與互動功能處理。

完成項目：

- Previous / Next controls
- Keyboard navigation
- Slide counter
- Progress indicator
- Source footer
- Project-relative source links

Programmatic Check：

- 12 slides：PASS
- Navigation script：PASS
- JavaScript syntax：PASS
- Slides 2–11 source footer：PASS
- 無 external HTTP assets：PASS
- 無 absolute local paths：PASS
- source links 使用 project-relative targets：PASS

固定 1440 × 900 layout validation：

**12 / 12 slides 無偵測到 overflow**

執行環境中的 Chromium screenshot 因 browser administrator policy 被阻擋，因此沒有將這個環境限制誤判成 deck error；改以 WeasyPrint fixed-page layout validation 檢查版面，並以 `node --check` 單獨驗證 navigation JavaScript。

---

## 8. 哪個節點最常出錯？

整體流程中，最容易出錯的不是 HTML Render，而是：

**Ingest / Fidelity Boundary**

主要錯誤類型包括：

1. 把來源沒有寫的合理細節補進去
2. 把兩個真的 source facts 拼成一個不存在的 composite case
3. 忽略 Appendix 導致誤判 evidence unsupported
4. 修改新 batch 時影響已通過舊 batch metadata
5. 將 internal project design 寫得比來源實際內容更具體

---

## 9. 問題在哪一個檢查節點被發現？

### Batch 2
問題在 ingest fidelity review 發現：
- prior-batch metadata regression
- appendix evidence coverage

### Batch 3
問題在 independent review 發現：
- Voyager composite case
- publication version / author metadata
- benchmark context mixing

### Batch 4
問題在 Project E.X.I.T. fidelity audit 發現：
- 大量 unsupported implementation details

### Slides
問題已在 Evidence Card 與 Outline 階段提前限制，因此 Slide Fidelity Check 第一次即通過。

---

## 10. 是否把檢查往前移？

**有。**

實際 workflow 經過執行後，將部分檢查提前：

### 1. Prior-batch regression guard
每完成新 batch，不只檢查新內容，也確認之前已 FINAL PASS 的 metadata 沒被修改。

### 2. Source coverage check
在判定 unsupported 前，先確認來源版本是否包含：

- main text
- appendix
- supplementary material

避免只讀主文就誤判。

### 3. Evidence Card source boundary
Evidence Card 階段先記錄：

- source type
- allowed interpretation
- limitation
- suggested slide

避免到 Slide Draft 才處理來源界線。

### 4. Internal source boundary
Project E.X.I.T. 在 Evidence Card 階段就限制為：

- Planned
- Internal Design
- component / responsibility mapping

因此 Slide 11 不再嘗試補出來源沒有定義的完整 orchestration flow。

---

## 11. Workflow 是否修改？

**有，但主要是增加前置檢查，不改變原本主要 Graph。**

原始 Graph 保留：

`Raw Sources → Ingest → Wiki → Retrieval → Evidence → Outline → Draft → Fidelity → Render`

實際執行後新增的檢查概念：

- prior-batch regression check
- source-version / appendix coverage check
- evidence boundary check
- internal vs external source classification
- component-level fidelity before slide generation

---

## 12. 修改原因

本專案最主要的風險不是「模型完全不知道內容」，而是：

**模型容易把合理推論寫得像來源事實。**

在研究論文與系統設計文件混用時，這個風險更明顯。

因此 Workflow 的修改重點不是增加更多生成步驟，而是：

**讓來源邊界更早被固定。**

這能減少後續 Slide Draft 與 HTML Render 再回頭大幅修稿的機會。

---

## 13. Final Result

目前完成：

- [x] 6 篇 external academic sources ingest
- [x] 1 份 Project E.X.I.T. internal design document ingest
- [x] 7 個 Wiki topics
- [x] Source registry
- [x] Coverage Check
- [x] Evidence Cards
- [x] 12-slide Outline
- [x] Human Outline Check
- [x] 12 Slide Drafts
- [x] Independent Fidelity Check
- [x] 12-slide HTML Deck
- [x] Programmatic Check
- [x] Render / overflow Check
- [x] Final Report

最終 HTML：

`slides/deck.html`

最終 Wiki / Slide workflow 能保持：

**Research Evidence → Wiki → Evidence Card → Slide**

以及：

**Internal Design → Application Mapping**

兩種來源角色分離。

---

## 14. Reflection

這次最大的收穫不是單純讓 LLM 幫忙整理文獻，而是發現「可追溯性」需要被設計進 Workflow。

只要求模型「不要亂寫」效果有限；更有效的方法是把工作拆成：

- small-batch ingest
- source registry
- Evidence Card
- coverage check
- independent fidelity check
- stop condition

每一層都縮小模型可以自由補充的空間。

實際執行中也發現，錯誤不一定是完全虛構。有時來源中的兩個內容都是真的，但被模型錯誤組合後仍會形成 false claim。因此後續如果再做類似專案，我會在 Evidence Card 中進一步保留 source location / figure / appendix 資訊，並在 ingest 完成後立即執行 regression check。

對 HTML Deck 而言，真正困難的部分反而不是排版，而是確保每一張投影片在進入排版前就已經具有明確 evidence boundary。只要 Slide Draft 已通過 fidelity review，後面的 Render 階段就能單純處理視覺與互動，不再修改研究內容。

整體來看，這次 Workflow 的主要改進方向是：

**把檢查往前移，讓錯誤在進入簡報生成前就被阻擋。**
