# Slide 5 — RAG：讓 Agent 查外部知識

## Main teaching point
RAG 的核心是「先檢索外部知識，再生成」，讓模型能利用可更新、可檢查的外部文件。

## Supporting explanation
Lewis et al. (2020) 將知識來源分成：

- **Parametric Memory**：模型權重中隱式儲存的知識。
- **Non-parametric Memory**：外部可檢索的文件索引。

原始 RAG 流程：

`Query → DPR Retrieval → Retrieved Documents → BART Generation`

外部索引可以直接替換，因此能更新知識內容，而不必重新訓練整個生成模型。

## Example / diagram idea
畫出：

`問題 → 外部知識庫 → 相關文件 → Generator → 回答`

## Limitation
Lewis et al. (2020) 的原始 RAG 採 DPR + BART 端到端架構；現代常見的 Vector DB + Prompt 實作則屬後續延伸形式。

## Source reference
- `slides/cards/04-rag.md`
- `wiki/rag.md`
- Lewis et al. (2020), *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*
