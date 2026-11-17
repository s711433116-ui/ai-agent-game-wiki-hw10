# Project E.X.I.T. Design Document

Source type: Internal Project Design Document

## Purpose
本文件只支援 Project E.X.I.T. 的應用案例，不作為一般性 AI Agent 研究證據。

## Core Loop
基地準備 → 接受任務 → 進入地圖 → 探索 / 搜刮 / 戰鬥 → AI 動態事件 → 撤離 → 結算 → 返回基地

## AI Director
預計讀取 Player HP、Inventory、Location、Mission progress、Remaining time、Base resources，再決定 Dynamic quest、Optional event、Risk warning 或 NPC response。

## Planned AI Techniques
- Tool Use：取得 Game State、Inventory、Map、Mission 等實際遊戲資料。
- RAG：讓 NPC 查詢世界觀、地圖、物品與角色知識。
- Planning：用於多步驟複合任務。
- Reflection：檢查任務是否可完成、是否與 Game State 衝突、獎勵是否合理。
- Agent Workflow：區分 AI Director、Quest Agent、NPC Agent 與 Game System。

## Boundary
角色移動、碰撞、HP 扣除、掉落機率、背包容量等明確規則由一般遊戲程式處理，不交給 LLM 即時決定。

## Status
以上內容屬於設計規劃，不代表所有功能已完成實作。
