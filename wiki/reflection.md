# Reflection（反思機制）

## 1. Overview
在 AI Agent 架構中，**Reflection（反思機制）**是指代理對自身的思考歷程、動作軌跡或環境反饋進行後設認知（metacognition）與語意評估，進而調整未來規劃與行為的能力（Wang et al., 2024; Shinn et al., 2023）。

傳統的單向決策流程在遭遇失敗或無效動作時，容易陷入重複錯誤或無法跳脫局部死結。Noah Shinn 等人於 2023 年提出的 **Reflexion** 框架，開創了一種名為**語言增強學習（Verbal Reinforcement Learning）**的範式：代理**完全不更新語言模型的神經網路權重**，而是將環境的標量或二元獎勵訊號轉譯為具體的自然語言自我反思文本，並將其保存在情節記憶緩衝區（Episodic Memory Buffer）中，以此作為「語意梯度（Semantic Gradient）」在後續試驗（trial）中引導更精確的決策（Shinn et al., 2023）。

> [!IMPORTANT]
> **概念層級特別釐清**：
> 1. **非傳統強化學習（No Gradient / No Weight Update）**：Reflexion 雖然借用強化學習（RL）的術語（Policy、Reward、Trial），但它**完全不涉及梯度下降或神經網路權重更新**。語言模型在推論期間參數完全凍結，政策是透過「凍結的 LLM 參數加上情節記憶文本編碼」來形式化。
> 2. **非所有反思等同於 Reflexion**：廣義反思包含單次輸出的文字自評（如 Self-Refine）；Reflexion 則是特指結合 Actor、Evaluator、Self-Reflection 與多輪試驗情節記憶的特定反思框架（Shinn et al., 2023; Wang et al., 2024）。

---

## 2. Core Concept
### (1) 語言增強學習 vs. 傳統強化學習（Verbal RL vs. Traditional RL）
Shinn et al.（2023）指出傳統基於策略或價值（Policy/Value-based）的 RL 方法在應用於大語言模型時面臨的挑戰：
- **傳統 RL**：依賴大量樣本採樣、計算標量或向量獎勵，並透過高成本的梯度下降微調龐大參數，且在語意空間中極難進行精準的信用分配（Credit Assignment Problem，即難以判斷長軌跡中究竟是哪一步動作導致失敗）。
- **Verbal Reinforcement Learning**：
  - **權重更新**：**無（Zero Parameter Update）**。模型參數完全維持原樣。
  - **政策參數化**：代理的政策 $\pi_\theta(a_t \mid s_t)$ 由「情節記憶編碼（$\text{mem}$）配對凍結的語言模型參數（$M_a$）」共同組成，即 $\theta = \{M_a, \text{mem}\}$。
  - **反饋形式**：將環境回傳的二元成敗或標量數值，轉化為豐富且具針對性的自然語言摘要。
  - **語意梯度（Semantic Gradient）**：文字反思直接為代理指出「上一輪犯了什麼錯、下一輪應該嘗試何種具體動作」，提供具備方向性的語意引導。

### (2) Reflexion 三核心組件模型
Reflexion 將代理系統模組化為三個協同運作的組件（Shinn et al., 2023）：
1. **Actor（行動者，$M_a$）**：
   - 負責根據當前狀態觀察 $o_t$ 與記憶上下文生成推理文字與具體動作 $a_t$。
   - 可採用 Chain of Thought（CoT）或 ReAct 等不同的動作生成提示架構。
2. **Evaluator（評估者，$M_e$）**：
   - 負責評估 Actor 所產生的軌跡品質，輸出成敗訊號或獎勵分數 $r_t = M_e(\tau_t)$。
   - 實作形式因任務而異：推理任務可採用精確匹配（Exact Match, EM）；決策任務可採用啟發式規則（Heuristics，如偵測到連續 3 次無效動作或步數超過 30 步）；程式碼任務可採用自建單元測試執行結果；亦可採用獨立的 LLM 進行二元分類評估。
3. **Self-Reflection（自我反思者，$M_{sr}$）**：
   - 由 LLM 充當，接收當前軌跡 $\tau_t$、評估獎勵 $r_t$ 以及既有記憶 $\text{mem}$，深入分析失敗原因並產生具有操作性的文字反思 $sr_t$（Verbal Experience Feedback）。

### (3) 短期記憶 vs. 長期情節記憶（Memory Architecture）
Reflexion 明確劃分兩種時間跨度的記憶（Shinn et al., 2023）：
- **短期記憶（Short-term Trajectory Memory）**：
  - 單一試驗內部記錄的當前軌跡序列 $\tau_t = [a_0, o_0, a_1, o_1, \dots, a_i, o_i]$，供 Actor 在當前 episode 內部維持上下文。
- **長期情節記憶（Long-term Episodic Memory, $\text{mem}$）**：
  - 跨試驗持久保留的自我反思摘要經驗池 $\text{mem} = [sr_0, sr_1, \dots]$。
  - 為避免超出語言模型的 context window 上限，實務上採用**滑動視窗限制**，將最大儲存經驗數 $\Omega$ 限制為最近的 1–3 筆反思。

---

## 3. Why It Matters
- **低成本試錯學習**：無需耗費昂貴算力重新訓練或微調模型權重，代理即可藉由數次「試錯－反思」在少量試驗中學會複雜任務（Shinn et al., 2023）。
- **可解釋的信用分配**：透過第一人稱的自然語言反思，人類與模型皆能清楚理解失敗的原因（例如「我以為我手上拿著平底鍋，但其實沒有」），大幅提升決策透明度（Shinn et al., 2023）。
- **化解長程軌跡盲目重複**：單純的 ReAct 代理在失敗後若重新開始，常會陷入相同的幻覺迴圈；導入反思記憶後，代理能在後續試驗中避開已證明無效的動作路徑（Shinn et al., 2023）。

---

## 4. How It Works
### (1) Reflexion 迭代優化演算法（Algorithm 1）
依據 Shinn et al.（2023）之 Algorithm 1：
1. **初始化**：設定 Actor $M_a$、Evaluator $M_e$、Self-Reflection $M_{sr}$，將政策初始化為 $\pi_\theta$（$\theta = \{M_a, \text{mem}\}$），初始記憶 $\text{mem} = \emptyset$。
2. **初始試驗**：Actor 生成初始軌跡 $\tau_0$；Evaluator 評估 $\tau_0$。若通過則終止；若失敗，$M_{sr}$ 分析 $\{\tau_0, r_0\}$ 產生反思文字 $sr_0$，存入 $\text{mem}$。
3. **迭代試驗迴圈（while $M_e$ not pass and $t < \text{max trials}$）**：
   - Actor 結合輸入問題與 $\text{mem}$ 中的歷史反思，生成新軌跡 $\tau_t$。
   - Evaluator 評估 $\tau_t$ 是否達成目標。若達成則成功終止。
   - 若未達成，$M_{sr}$ 根據新軌跡與失敗結果生成反思 $sr_t$。
   - 將 $sr_t$ 追加至 $\text{mem}$（超出 $\Omega$ 限制時淘汰最舊記憶）。
   - $t \leftarrow t + 1$。

### (2) 程式碼任務中的自驗證機制（Test-driven Self-Reflection）
在程式碼生成任務中，Reflexion 採用基於單元測試的自評機制（Shinn et al., 2023）：
- 代理利用 CoT 生成多樣化內部單元測試，並透過抽象語法樹（AST）過濾合法測試，挑選至多 6 個測試案例作為測試套件 $T = \{t_0, \dots, t_n\}$。
- 程式執行若未通過單元測試，編譯器報錯或測試斷言失敗會直接作為客觀評估訊號，促使 $M_{sr}$ 針對具體錯誤進行反思。

### (3) Reflection 機制來源觀點對照（Wiki 比較性分析）
> [!NOTE]
> 本段落將本專案已 Ingest 之綜述 `01-llm-agent-survey.pdf`、反思專研 `05-reflexion.pdf` 與具身代理 `06-voyager.pdf` 的反思概念進行跨來源對照，為本 Wiki 的比較性分析整理。

- **綜述回顧視角（Survey Review: Wang et al., 2024）**：
  - 將反思視為記憶操作之一（如 Generative Agents 中定時從既有記憶萃取高階洞見）以及閉環規劃的重要分支（Planning with Feedback）。
  - 將 Reflexion、Self-Refine 等並列為模型內部回饋（Model Feedback / Self-Feedback）的代表範式。
- **語言增強學習視角（Primary Research: Shinn et al., 2023）**：
  - 聚焦於「多輪試錯（Multi-trial）」與「跨試驗情節記憶（Episodic Memory Buffer）」，將反思嚴格定義為提供語意梯度的強化學習機制。
- **具身批判與驗證視角（Primary Research: Wang et al., 2023 - Voyager）**：
  - 在具身環境中提出 **Self-Verification（自我驗證）**機制。相較於純自我反思，Voyager 指出自我驗證更為全面：由獨立 GPT-4 Critic 同時負責判斷任務是否真正成功（避免過早退出），並在失敗時給出批判（Critique）以引導程式碼修正。

---

## 5. Example
### (1) AlfWorld 居家文字決策任務（Shinn et al., 2023）
- **任務目標**：「用檯燈檢查馬克杯（examine the mug with the desklamp）」。
- **Trial 1 失敗**：代理先在抽屜與桌面上找到馬克杯拿在手上，接著尋找檯燈，但在 desk 2 上找不到檯燈而反覆嘗試無效動作，最終逾時失敗。
- **反思產出（$sr_0$）**：
  > 「我的計畫是先找馬克杯再找檯燈。但本任務要求用檯燈檢查馬克杯，我應該先找檯燈再找馬克杯。我注意到 desk 1 上有檯燈。下一輪我會直接去 desk 1 找到檯燈，再去拿馬克杯並用檯燈檢查。」
- **Trial 2 成功**：代理讀取反思記憶，第一步直奔 desk 1 取得檯燈位置，順暢完成檢查任務。
- **評測結果**：在 134 個 AlfWorld 任務中，ReAct + Reflexion 成功解決 130 個任務（97%），相較於 ReAct 基線在 12 次迭代中取得 **絕對值 22% 的提升**。

### (2) HotPotQA 多跳推理問答（Shinn et al., 2023）
評測 100 道多跳問答題。論文在不同脈絡下報告了兩組關鍵成果：
- **整體基準改善**：論文整體摘要指出，Reflexion 在 HotPotQA 推理問答任務上，相較於強基線方法取得最高約 **20 個百分點（20 percentage points）**的表現提升。
- **純推理分析設定（Reasoning-only CoT with Ground Truth Context）**：在提供真實上下文（$C_{gt}$）以隔離純推理能力的分析設定中，CoT(GT) 代理原本有 39% 的問題無法正確推斷答案，而 Reflexion 代理在未給予標準答案的前提下，透過自我反思修正錯誤，將準確率提升了約 **14 個百分點（14 percentage-point improvement）**。

### (3) 程式碼生成基準評測（Shinn et al., 2023 - Table 1）
- **HumanEval (Python)**：Reflexion 達到 **91.0% pass@1**，超越當時 GPT-4 基準的 80.1% 與先前 SOTA（CodeT + GPT-3.5 的 65.8%）。
- **HumanEval (Rust)**：Reflexion 達到 **68.0% pass@1**（GPT-4 基準為 60.0%）。
- **MBPP (Rust)**：Reflexion 達到 **75.4% pass@1**（GPT-4 基準為 70.9%）。
- **LeetcodeHard (Python)**：Reflexion 達到 **15.0% pass@1**（GPT-4 基準為 7.5%）。

---

## 6. Limitations / Notes
依據 Shinn et al.（2023）論文之實驗與消融分析，Reflexion 存在以下明確限制：
1. **反思能力屬於較大較強模型之湧現特性（Emergent Quality）**：
   - 論文 Table 4 針對較小開源模型 **starchat-beta** 進行實驗，結果顯示基準準確率為 0.26，加入 Reflexion 8 次試驗後的平均準確率仍為 **0.26**（完全無提升）。
   - 此實驗結果支持作者的觀察：有效的 self-correction 較可能是較強、較大型模型才展現的 emergent quality；不能由單一模型實驗推論成所有模型尺寸的普遍定律。
2. **MBPP Python 上的效能倒退與偽陽性測試（Flaky Unit Tests）**：
   - 論文 Table 1 顯示，在 **MBPP (Python)** 上，Reflexion 的 pass@1 僅為 **77.1%**，反而**低於 GPT-4 基線的 80.1%**。
   - 原因分析：自建單元測試存在偽陽性（False Positive）。MBPP Python 的偽陽性率高達 16.3%（相較 HumanEval 僅 1.4%），導致錯誤的程式碼通過了自我測試而過早提交失敗答案。
3. **無法跳脫高探索需求之局部最佳解（WebShop 實驗失敗）**：
   - 在需要高度探索多樣性與創意的電商購物基準 **WebShop** 中，ReAct + Reflexion 在 4 次試驗後完全沒有顯著進步（Figure 6），作者提前終止實驗。因為反思容易在失敗後產生無效或重複的推理，難以跳脫局部最佳解（Local Minima）。
4. **缺乏形式化成功保證（No Formal Guarantee）**：
   - 與符號規劃器不同，語言反思本質上依賴啟發式自評與大模型推理，無法形式化保證最終一定能找到正確解。
5. **記憶容量與成本限制**：
   - 受限於 context window，反思記憶必須維持在 $\Omega \approx 1-3$ 的滑動視窗；且多輪試驗顯著增加了 API 呼叫次數與推論延遲。

---

## 7. References
- **Shinn et al. (2023)**: Noah Shinn, Federico Cassano, Edward Berman, Ashwin Gopinath, Karthik Narasimhan, Shunyu Yao. *Reflexion: Language Agents with Verbal Reinforcement Learning*. arXiv preprint arXiv:2303.11366v4 [cs.AI], 10 Oct 2023. [05-reflexion.pdf](../raw/05-reflexion.pdf)（註：後續正式發表於 Advances in Neural Information Processing Systems 36 (NeurIPS 2023), Main Conference Track，正式版作者名單未列 Edward Berman）。
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024, 18(6): 186345. (DOI: 10.1007/s11704-024-40231-1; arXiv:2308.11432v7). [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
- **Wang et al. (2023)**: Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar, Chaowei Xiao, Yuke Zhu, Linxi Fan, Anima Anandkumar. *VOYAGER: An Open-Ended Embodied Agent with Large Language Models*. arXiv preprint arXiv:2305.16291v2 [cs.AI], 19 Oct 2023. [06-voyager.pdf](../raw/06-voyager.pdf)（註：後續正式發表於 Transactions on Machine Learning Research (TMLR), March 2024）。
