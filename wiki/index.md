# AI Agent 與生成式 AI 遊戲系統 Wiki

歡迎閱讀以「AI Agent 與生成式 AI 遊戲系統」為主題的知識庫。本 Wiki 嚴格依據 [AGENTS.md](../AGENTS.md) 規範，採小批次 Ingest 流程，所有重要論點皆標註來源並可追溯至原始文獻，絕不使用模型未經證實之常識補齊內容。

---

## Wiki 知識庫目錄

| 主題檔案 | 涵蓋核心內容 | 當前狀態 | 支援來源 (Ingested) |
|---|---|---|---|
| [ai-agent.md](ai-agent.md) | AI Agent 基本概念、四大模組架構（Profile, Memory, Planning, Action）、能力獲取途徑 | **已更新 (Batch 1)** | `01-llm-agent-survey.pdf`, `02-toolformer.pdf` |
| [tool-use.md](tool-use.md) | Tool Use 概念、自監督工具學習、API 線性化格式、解碼機制與規模法則、限制 | **已更新 (Batch 1)** | `02-toolformer.pdf`, `01-llm-agent-survey.pdf` |
| [agent-workflow.md](agent-workflow.md) | Agent Workflow 概念、單路徑/多路徑/回饋閉環/多代理協同流程、工具與規劃子流程、試錯/具身與遊戲應用工作流 | **已更新 (Batch 1–4)** | `01-llm-agent-survey.pdf`, `02-toolformer.pdf`, `03-rag.pdf`, `04-llm-plus-p.pdf`, `05-reflexion.pdf`, `06-voyager.pdf`, `project-exit-design.md` |
| [rag.md](rag.md) | 檢索增強生成（RAG）、參數化/非參數化記憶、RAG-Sequence/Token 模型、聯合訓練與熱插拔 | **已更新 (Batch 2)** | `03-rag.pdf` |
| [planning.md](planning.md) | 規劃機制（Planning）、語言能力 vs 功能性能力、PDDL 形式化、LLM+P 外部規劃器管線 | **已更新 (Batch 2)** | `04-llm-plus-p.pdf`, `01-llm-agent-survey.pdf` |
| [reflection.md](reflection.md) | 反思機制（Reflection）、語言增強學習（Verbal RL）、Reflexion 三核心模型、情節記憶緩衝區 | **已更新 (Batch 3)** | `05-reflexion.pdf`, `01-llm-agent-survey.pdf`, `06-voyager.pdf` |
| [game-application.md](game-application.md) | 生成式 AI 遊戲系統應用、Voyager Research Case 與 Project E.X.I.T. Internal Application Case（含 Planned / deterministic boundary） | **已完成 (Batch 4)** | `06-voyager.pdf`, `project-exit-design.md`, `01-llm-agent-survey.pdf` |

---

## 已 Ingest 來源清單
完整來源元資料請參閱 [slides/sources.md](../slides/sources.md)：
### Batch 1
1. **01-llm-agent-survey.pdf**: Wang et al. (2024), *A Survey on Large Language Model based Autonomous Agents*, Frontiers of Computer Science, 18(6): 186345. (arXiv:2308.11432v7, revised 2025-03-02).
2. **02-toolformer.pdf**: Timo Schick, Jane Dwivedi-Yu, Roberto Dessì, Roberta Raileanu, Maria Lomeli, Eric Hambro, Luke Zettlemoyer, Nicola Cancedda, Thomas Scialom (2023), *Toolformer: Language Models Can Teach Themselves to Use Tools*, NeurIPS 2023.

### Batch 2
3. **03-rag.pdf**: Patrick Lewis et al. (2020), *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*, NeurIPS 2020.
4. **04-llm-plus-p.pdf**: Bo Liu et al. (2023), *LLM+P: Empowering Large Language Models with Optimal Planning Proficiency*, arXiv:2304.11477v3 [cs.AI].

### Batch 3
5. **05-reflexion.pdf**: Noah Shinn et al. (2023), *Reflexion: Language Agents with Verbal Reinforcement Learning*, arXiv:2303.11366v4 [cs.AI]. (正式發表於 NeurIPS 2023).
6. **06-voyager.pdf**: Guanzhi Wang et al. (2023), *VOYAGER: An Open-Ended Embodied Agent with Large Language Models*, arXiv:2305.16291v2 [cs.AI]. (正式發表於 TMLR 2024).

### Batch 4 (Internal Source)
7. **project-exit-design.md**: *Project E.X.I.T. Design Document* (Internal Project Design Document / Application Case Source).

---

## 執行紀錄
執行紀錄與檢查日誌請參閱 [log.md](../log.md)。
