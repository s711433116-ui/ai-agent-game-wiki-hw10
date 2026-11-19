# Evidence Card 04 — RAG

- **Topic**: RAG
- **Claim / Teaching Point**: Retrieval-Augmented Generation 將模型參數中的 Parametric Memory 與外部可檢索的 Non-parametric Memory 結合，使生成模型能先檢索相關文件，再利用文件內容生成答案。
- **Supporting Source**:
  - `wiki/rag.md`
  - Lewis et al. (2020), *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*
- **Evidence Summary**:
  - 原始 RAG 使用 DPR 檢索外部 Wikipedia 向量索引，再由 BART Generator 生成。
  - 非參數化記憶可透過替換文件索引更新知識。
  - 論文提出 RAG-Sequence 與 RAG-Token 兩種形式。
- **Allowed Interpretation**: 可用「先查外部知識，再生成」作為教學直覺；也可說明 RAG 能讓知識來源比只靠模型權重更容易更新與檢查。
- **Limitations**:
  - 2020 原始 RAG 是 DPR + BART 的端到端可微分模型。
  - 現代常見的「向量資料庫 + Embedding + Prompt」是後續廣義 RAG 用法，不應直接說成 Lewis et al. (2020) 原始架構。
  - 原始 RAG 並未設計多輪 query reformulation。
- **Suggested Slide**: Slide 5 — RAG
