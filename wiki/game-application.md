# Game Application（遊戲系統應用）

> [!IMPORTANT]
> **批次範圍與來源聲明**：
> 本頁面目前僅涵蓋已 Ingest 之外部學術研究案例——**Voyager in Minecraft（Wang et al., 2023）**。自建遊戲系統專案案例（Project E.X.I.T. 設計文件）將於後續內部來源批次正式 Ingest 後補充，目前嚴禁提前寫入。

---

## 1. Overview
在生成式 AI 與遊戲系統的交叉領域中，將具備推理與工具使用能力的大型語言模型（LLM）引入遊戲環境，正從傳統的固定腳本 NPC 推進為具備自主探索、技能學習與環境互動能力的**具身遊戲代理（Embodied Game Agent）**（Wang et al., 2023; Wang et al., 2024）。

傳統強化學習（RL）在開放世界遊戲中常面臨探索效率低落、獎勵極度稀疏以及難以跨任務累積技能等瓶頸；而早期基於 LLM 的遊戲代理則多受限於靜態規劃或單次對話，無法像人類玩家一樣在長達數十小時的遊戲過程中持續獲取、固化並複用新技能。Guanzhi Wang 等人於 2023 年提出的 **VOYAGER**，是首個在開放世界沙盒遊戲《當個創世神》（Minecraft）中實現的**終身學習具身代理（Embodied Lifelong Learning Agent）**，展示了語言模型在無需更新神經網路權重的前提下，如何透過自動課綱、程式碼技能庫與迭代提示機制自主探索開放世界（Wang et al., 2023）。

---

## 2. Core Concept
### (1) 開放世界沙盒遊戲的環境特性（Open-Ended Embodied World）
以 Minecraft 為代表的開放世界沙盒環境具備以下特點（Wang et al., 2023）：
- **無預設單一終點目標（No Predefined End Goal）**：遊戲不提供固定的關卡路徑或劇情主線，玩家置身於程序化生成的 3D 無垠地形中，依賴自我驅動探索。
- **階層化科技樹（Hierarchical Tech Tree）**：遊戲生存具備嚴格的資源與工具進階關係（木製工具 $\to$ 石製工具 $\to$ 鐵製工具 $\to$ 鑽石工具），需要高度結構化與時間延展性的複合技能。
- **高自由度動作空間**：包含採礦、建造、合成、冶煉、戰鬥、農耕等龐大活動光譜。

### (2) 具身終身學習（In-context Lifelong Learning）
傳統機器學習在跨任務學習時極易發生**災難性遺忘（Catastrophic Forgetting）**；VOYAGER 提出一種上下文終身學習範式，能有效緩解（alleviates）災難性遺忘：
- 語言模型（GPT-4）本身作為黑盒推論引擎，完全維持參數量與權重不變（Gradient-Free）。
- 技能庫可持續累積並重用既有技能，使後續技能能建立在既有技能之上，並在不同世界間遷移重用（Wang et al., 2023）。

### (3) 程式碼即動作空間（Code as Action Space）
不同於傳統遊戲 AI 採用的底層動作指令（如鍵盤按鍵、滑鼠微觀位移或離散動作編號），VOYAGER 選擇**可執行的 JavaScript 程式碼（基於 Mineflayer API）作為動作空間**（Wang et al., 2023）：
- **時間延展性（Temporally Extended Actions）**：單一程式碼函數（如 `mineBlock` 或 `smeltItem`）可封裝長達數十秒或數分鐘的複雜連續操作。
- **可解釋性與可讀性（Interpretable）**：程式碼語義清晰，便於人類檢視與語言模型自我修正。
- **組合性（Compositional）**：高階技能可直接調用既有低階技能函數，形成階層式能力疊加。

---

## 3. Why It Matters
- **化解開放世界探索停滯**：在缺乏外部顯式獎勵時，純 ReAct 或 AutoGPT 容易在原地迷航或陷入無效動作；藉由自動課綱引導與新奇度驅動，代理能自主解鎖整套遊戲科技樹（Wang et al., 2023）。
- **技能持久累積與零樣本遷移**：透過將技能封裝為程式碼並以向量索引儲存，代理在面對全新生成的 Minecraft 世界時，能直接檢索既有技能庫，零樣本快速完成各類全新合成任務（Wang et al., 2023）。
- **模組化提示優於模型微調**：直接透過 Prompting 結合環境反饋與自評機制，繞過了龐大模型微調的運算門檻與資料收集成本（Wang et al., 2023）。

---

## 4. How It Works
VOYAGER 由三大核心組件協同構成（Wang et al., 2023）：

```text
       ┌────────────────────────┐
       │  Automatic Curriculum  │ (GPT-4 自主由下而上提議挑戰任務)
       └───────────┬────────────┘
                   │ 新任務 (New Task)
                   ▼
┌──────────────────────────────────────┐        檢索相關技能
│     Iterative Prompting Mechanism    │ ◄───────────────────────┐
│  - Code Generation (GPT-4)           │                         │
│  - Environment Feedback (Mineflayer) │                         │
│  - Execution Errors (Interpreter)    │                         │
│  - Self-Verification Critic (GPT-4)  │                         │
└──────────────────┬───────────────────┘                         │
                   │ 驗證成功 (Success)                           │
                   ▼                                             │
       ┌────────────────────────┐                                │
       │     Skill Library      │ (向量資料庫儲存程式碼與描述) ─────┘
       └────────────────────────┘
```

### (1) Automatic Curriculum（自動課綱）
- **運作原理**：利用 GPT-4 的常識知識庫，以「盡可能發現多樣化的事物（discover as many diverse things as possible）」為目標，自主生成由淺入深的漸進任務。
- **動態狀態感知**：提議新任務時綜合考量代理目前狀態（背包道具清單、裝備、周遭 32 格內的方塊與生物、所處生物群落 Biome、生命與飢餓值、座標）以及已完成/失敗的任務歷史。
- **由下而上開展（Bottom-up Exploration）**：例如若身處河邊且持有釣竿，課綱會主動提議「釣 1 隻魚」；若持有生鐵與熔爐，則提議「冶煉 4 塊鐵錠」。

### (2) Skill Library（技能庫）
- **技能儲存（Add New Skill）**：每當代理成功完成一項新任務並通過驗證，系統利用 GPT-3.5 為該 JavaScript 函數生成簡要文本描述，將**描述之 Text Embedding（text-embedding-ada-002）作為 Key**，**可執行程式碼作為 Value**，寫入向量資料庫。
- **技能檢索（Skill Retrieval）**：當課綱提出新任務時，系統結合任務提示與環境現況生成查詢向量，自技能庫中檢索相關度最高的 **Top-5 技能程式碼**，注入 GPT-4 的生成上下文供其 In-context 組合重用。

### (3) Iterative Prompting Mechanism（迭代提示機制）
代理在生成控制程式碼時，常無法一次成功（one-shot failure）；VOYAGER 建立多重回饋自我修正迴圈（至多 4 輪生成嘗試）：
1. **Environment Feedback（環境反饋）**：Mineflayer API 在底層執行時，透過 `bot.chat()` 回報中間狀態與失敗原因（例如「無法合成鐵胸甲，尚缺 7 塊鐵錠」）。
2. **Execution Errors（執行報錯）**：程式解譯器攔截 JavaScript 執行階段錯誤（如呼叫不存在之 API 或語法例外），提供具體 traceback 引導除錯。
3. **Self-Verification Critic（自我驗證批評器）**：由獨立的 GPT-4 實例擔任評判角色，檢視執行後的遊戲狀態與目標需求，判定任務是否真正完成；若判定失敗，Critic 會給出具體的診斷建議（Critique，例如「你只殺了 2 隻羊，還需要再殺 1 隻羊以完成任務」）。

---

## 5. Research Case: Voyager in Minecraft
Wang et al.（2023）在開源 Minecraft AI 框架 **MineDojo** 上，將 VOYAGER 與 ReAct、Reflexion、AutoGPT 等先進代理架構進行系統性對比：

### (1) 科技樹解鎖能力（Tech Tree Mastery，Table 1）
- **木製工具**：VOYAGER 達成速度為基線（AutoGPT）的 **15.3 倍**（平均 6 次提示迭代完成，AutoGPT 需 92 次）。
- **石製工具**：達成速度為基線的 **8.5 倍**（VOYAGER 需 11 次，AutoGPT 需 94 次）。
- **鐵製工具**：達成速度為基線的 **6.4 倍**（VOYAGER 需 21 次，AutoGPT 需 135 次）。
- **鑽石工具**：**VOYAGER 是全體對比模型中唯一成功解鎖鑽石工具的代理**（平均 102 次迭代完成，其餘 ReAct、Reflexion、AutoGPT 成功率皆為 0/3）。

### (2) 開放世界探索表現（Exploration Performance）
- **道具獲取多樣性**：在 160 次提示迭代中，VOYAGER 發現並收集了 **63 種獨特道具**，為其他對比代理的 **3.3 倍**。
- **地圖遍歷距離**：VOYAGER 穿越了平原、沙漠、熱帶雨林、滴石溶洞等多元地形，移動距離為基線的 **2.3 倍**。

### (3) 新世界零樣本泛化（Zero-shot Generalization，Table 2）
- 清空代理背包並隨機重置至全新 Minecraft 地圖，測試未曾見過的高階任務（如「製造金劍」、「採集岩漿桶」、「製作指南針」）。
- **結果**：具備歷史技能庫的 VOYAGER 在 50 次迭代限制內，四項未曾見過的任務在三次試驗中皆為 3/3 成功（平均需 18–21 次提示迭代，成功率標示為 3/3 trials per task）；而無技能庫的基線代理均宣告失敗（0/3 成功率）。若將 VOYAGER 積累的技能庫直接外掛賦予 AutoGPT，AutoGPT 在部分任務取得原本沒有的成功結果（例如在 Diamond Pickaxe 與 Golden Sword 達成 1/3 成功，在 Compass 達成 2/3 成功），顯示技能庫具有可移植與即插即用（plug-and-play）的實用潛力。

---

## 6. Limitations / Notes
依據 Wang et al.（2023）論文第 4 節指出的限制與觀察：
1. **運算與 API 費用高昂（Significant Cost）**：
   - VOYAGER 核心程式碼生成高度依賴 GPT-4 的推理能力（消融實驗中 GPT-3.5 程式碼生成表現大幅落後 5.7 倍），而 GPT-4 API 費用比 GPT-3.5 貴達 15 倍。
2. **缺乏視覺感知（No Visual Perception）**：
   - 論文實作受限於純文字 GPT-4 API，無法直接處理第一人稱視角之遊戲畫面（Screen Pixels），所有環境感知完全依賴 Mineflayer 結構化狀態抽取與文字描述；對於需要精確空間幾何判斷的 3D 建築結構（如建造地獄傳送門或房屋），必須藉由人類額外提供視覺批判（Human as a critic）。
3. **模型幻覺（Hallucination in Code & Curriculum）**：
   - 自動課綱有時會提議遊戲中根本不存在的虛構物品（如「銅劍 copper sword」、「銅胸甲 copper chestplate」）。
   - 程式碼生成時，GPT-4 有時會將圓石（cobblestone）作為熔爐燃料，或調用 Mineflayer 未提供的無效 API 函數。
4. **驗證器失效（Self-Verification Failures）**：
   - 自評模組偶爾會產生誤判（例如未將擊敗蜘蛛後掉落的「蜘蛛絲」正確識別為討伐成功的證據）。
5. **不應直接推論至其他遊戲系統（Wiki 來源邊界詮釋 / Wiki source-boundary interpretation）**：
   - VOYAGER 的實驗建立在 Minecraft / MineDojo 環境、Mineflayer 高階 JavaScript API，以及結構化與文字形式的環境狀態回饋上。原研究未評估缺乏等價程式控制介面、需要不同感知方式或不同控制粒度的其他遊戲類型，因此目前不能由 Voyager 的 Minecraft 結果直接推論其對其他遊戲系統的適用性。

---

## 7. References
- **Wang et al. (2023)**: Guanzhi Wang, Yuqi Xie, Yunfan Jiang, Ajay Mandlekar, Chaowei Xiao, Yuke Zhu, Linxi Fan, Anima Anandkumar. *VOYAGER: An Open-Ended Embodied Agent with Large Language Models*. arXiv preprint arXiv:2305.16291v2 [cs.AI], 19 Oct 2023. [06-voyager.pdf](../raw/06-voyager.pdf)（註：後續正式發表於 Transactions on Machine Learning Research (TMLR), March 2024）。
- **Wang et al. (2024)**: Lei Wang, Chen Ma, Xueyang Feng, Zeyu Zhang, Hao Yang, Jingsen Zhang, Zhi-Yuan Chen, Jiakai Tang, Xu Chen, Yankai Lin, Wayne Xin Zhao, Zhewei Wei, Ji-Rong Wen. *A Survey on Large Language Model based Autonomous Agents*. Frontiers of Computer Science, 2024, 18(6): 186345. [01-llm-agent-survey.pdf](../raw/01-llm-agent-survey.pdf)
