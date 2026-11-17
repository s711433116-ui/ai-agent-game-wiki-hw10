# AI Agent 與生成式 AI 遊戲系統 Wiki

歡迎閱讀以「AI Agent 與生成式 AI 遊戲系統」為主題的知識庫。本 Wiki 嚴格依據 [AGENTS.md](../AGENTS.md) 規範，採小批次 Ingest 流程，所有重要論點皆標註來源並可追溯至原始文獻，絕不使用模型未經證實之常識補齊內容。

---

## Wiki 知識庫目錄

| 主題檔案 | 涵蓋核心內容 | 當前狀態 | 支援來源 (Ingested) |
|---|---|---|---|
| [ai-agent.md](ai-agent.md) | AI Agent 基本概念、四大模組架構（Profile, Memory, Planning, Action）、能力獲取途徑 | **已更新 (Batch 1)** | `01-llm-agent-survey.pdf`, `02-toolformer.pdf` |
| [tool-use.md](tool-use.md) | Tool Use 概念、自監督工具學習、API 線性化格式、解碼機制與規模法則、限制 | **已更新 (Batch 1)** | `02-toolformer.pdf`, `01-llm-agent-survey.pdf` |
| [agent-workflow.md](agent-workflow.md) | Agent Workflow 概念、單路徑/多路徑/回饋閉環/多代理協同流程、工具調用工作流 | **已更新 (Batch 1)** | `01-llm-agent-survey.pdf`, `02-toolformer.pdf` |
| [rag.md](rag.md) | 檢索增強生成（Retrieval-Augmented Generation） | *等待後續批次 Ingest* | - |
| [planning.md](planning.md) | 規劃機制（Planning without/with feedback, Tree/Graph of Thoughts） | *等待後續批次 Ingest* | - |
| [reflection.md](reflection.md) | 反思機制（Memory Reflection, Self-Refine, Reflexion） | *等待後續批次 Ingest* | - |
| [game-application.md](game-application.md) | 生成式 AI 遊戲系統應用、Project E.X.I.T. 設計案例 | *等待後續批次 Ingest* | - |

---

## 第一批已 Ingest 來源清單
完整來源元資料請參閱 [slides/sources.md](../slides/sources.md)：
1. **01-llm-agent-survey.pdf**: Wang et al. (2024), *A Survey on Large Language Model based Autonomous Agents*, Frontiers of Computer Science. (arXiv:2308.11432v7, revised 2025-03-02).
2. **02-toolformer.pdf**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom (2023), *Toolformer: Language Models Can Teach Themselves to Use Tools*, NeurIPS 2023.

---

## 執行紀錄
執行紀錄與檢查日誌請參閱 [log.md](../log.md)。
