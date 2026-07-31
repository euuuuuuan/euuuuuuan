## AI 代理編排 · 確定性閘門 · AI 輔助開發

[English](README.md) · [한국어](README.ko.md) · [日本語](README.ja.md) · [简体中文](README.zh-CN.md) · **繁體中文** · [Español](README.es.md) · [Português (BR)](README.pt-BR.md) · [Deutsch](README.de.md) · [Français](README.fr.md) · [Русский](README.ru.md) · [Italiano](README.it.md) · [Bahasa Indonesia](README.id.md) · [Türkçe](README.tr.md)

以程式碼為本的自動化與代理編排系統：具備 fail-closed 權限層的代理平台、附評估閘門的本機檢索管線，
以及能逐位元重播的確定性模擬。

本頁的每一個數字都是量測值，不是估計值。產生該數字的指令就放在它所描述的儲存庫裡，
而且儲存庫在發布前會重新驗證一次。

---

## 旗艦作品

| 專案 | 這是什麼 | 量測到的事實 | 線上 |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | 本機優先的編排平台，用於有邊界的代理工作：一個受 fail-closed 權限閘門保護的 Swift macOS 控制常駐程式，由 TypeScript 編排器透過 MCP 驅動。*（平台）* | `docs/TOOL_SURFACE.json` 中記載 **63 個有文件的 MCP 工具**、**234 個測試／規格檔案**、**15 份 ADR**，以及一個遠端核准仲介（broker），其授權與指紋綁定且僅限單次使用（ADR-003）。 | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | 為網路隔離（망분리）環境打造的韓文 RAG：純標準函式庫的混合式檢索、PII 遮罩，以及針對超出範圍問題的拒答閘門。*（工具）* | **0 個第三方相依套件**，且建置由 **50 題黃金題組**把關：hit@3 **100 %**（39/39）、拒答精確率 **100 %**／召回率 **81.8 %** — 至於弱項指標「引用準確率 **36.75 %**」，選擇公開而不是藏起來。 | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | 讓代理透過 MCP 執行生命週期／CRM 行銷人員的 SQL 工作，背後的治理核心決定代理能走多遠、哪些事必須留下紀錄。*（工具）* | **108 個測試通過**（在安裝 MCP SDK 的前提下），其中包含對核心發動的 **33 個 PII 紅隊**與 **12 個治理紅隊**攻擊。資料 **100 % 合成**且可逐位元組重現：seed 42 → 相同的 SQLite 摘要、5,000 名客戶／99,922 筆事件。 | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | 在瀏覽器裡運行的戰術破門規劃器與即時小隊模擬；打完的突襲可以用一條 URL 分享，在別人的瀏覽器中重新模擬。*（可遊玩的建置，非商業發行）* | **30 Hz 固定 tick 模擬**，純度閘門在模擬與內容套件中禁用 **11 個非確定性 API** — 閘門通過，正是這一點讓 seed + 輸入紀錄得以完全一致地重播。**55 個測試／規格檔案**，`tools/` 裡有 40 個腳本，其中 35 個是無頭測試工具（其餘 5 個是素材抓取器與程式碼產生器）。 | **[試玩](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | 一部確定性推理裝置：8 個 MCP 工具，強制範圍鎖定、最低成本反證步驟計畫、盲點掃描，以及一道會駁回「缺乏執行證明之主張」的裁決閘門。*（工具）* | **0 個執行期相依套件**、**33/33 測試通過** — 其中一個測試本身就是保證：整個工具介面不寫入檔案系統、不產生子程序、也不發出網路呼叫。 | — |

---

## 這些專案是怎麼打造的

有趣的部分不在於「用 AI 打造」，而在於模型與儲存庫*之間*放了什麼。

- **兩個編碼代理，一個確定性裁判。** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)
  是一個外部驅動程式，讓兩個代理 CLI 互相對跑，直到閘門通過為止。「完成」由閘門判定 —
  絕不採信代理對自己工作的評價。12 個單元測試，在 `PATH` 縮減到只剩系統目錄的情況下照樣通過，
  因此即使兩個代理都沒安裝，驅動程式的拒絕行為依然可以被證明。
- **確定性閘門在呼叫模型之前就先跑。** 靠的是純度檢查器，不是感覺：
  fatal-funnel 的模擬閘門禁用 11 個非確定性 API；reasonforge 的唯讀測試強制 6 種禁用模式。
  檢索品質同樣是建置閘門 — 黃金題組加上門檻值，一旦退步就讓整次執行失敗
  （[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)）。
- **UI 的宣稱來自探針與截圖，不是來自記憶。** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)
  內建 `shot`、`relic-qa` 與 `mobile-qa` 測試工具；fatal-funnel 在 `tools/` 的 40 個腳本中
  有 35 個無頭測試工具，外加 4 台固定截圖攝影機。這個教訓是付出高昂代價才學到的：
  一個「會動」的工具提示，可能因為一個輸入過濾旗標而整個失效；合成輸入探針抓得出來，光讀程式碼抓不出來。
- **素材在生成之前就先過授權閘門。** [assetforge](https://github.com/euuuuuuan/assetforge-public)
  拒絕用任何不在其註冊表內的模型進行生成。往下游看，**已發布的 27 個快照全數附有 `CREDITS.md`，
  內含 4 級再散布對照表** — 任何人 fork 時都能確切知道自己必須移除什麼。
- **誠實的標籤，由 schema 強制執行。** 在 [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)
  中，每個公開的指標都定型為 `kind: z.enum(['measured', 'estimated'])`。任何數字都必須先聲明
  自己屬於哪一類才能發布。正是這條規則，讓本頁看不到任何湊整的行銷數字。
- **發布是一條管線，不是複製貼上。** 這裡的每個公開儲存庫都出自可重複執行的淨化發布流程，
  其宣稱檔會重新執行 README 裡的每一個數量，只要有一個出現偏差就拒絕建立 commit。
  27 個快照合計 **629 個測試／規格檔案**。

---

## 精選作品

不是全部 27 個儲存庫都列 — 只挑能展現一項獨特能力的。

**瀏覽器遊戲（點開連結就能玩）**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — 確定性戰術模擬，40 種武器、12 個手工設計的任務 · [線上試玩](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — 精巧的動作 RPG：彈反、遺物、兩個區域。*目前為 v0.5.1 垂直切片* · [線上試玩](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — 以排班人力比例為主題、小而完整的放置遊戲，18 個測試檔案 · [線上試玩](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — 等距視角 ARPG 切片。**與 hollowmere 共用同一個確定性模擬核心**；戰鬥規則、遭遇戰與內容則是它自己的。 · [線上試玩](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — 三波次等距視角競技場：看懂攻擊預兆、串連 mooncut、用衝刺躲過已出手的重擊。**43 個測試檔案**，加上全系列最嚴格的素材帳本 — 100 筆逐檔 SHA-256 紀錄，全部 CC0。 · [線上試玩](https://moonshard-warden.vercel.app)

**引擎遊戲（Godot / Unity — 垂直切片與原型，皆未商業發行）**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — 太空主力艦炮戰：砲彈飛行時間、慣性提前量、裝甲入射角穿深，跑在確定性 30 Hz 模擬上（27 個測試檔案）
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — 單人首領突襲 ARPG，十個三階段首領；「團隊絕不會替你通關」這個宣稱是戰後量測出的統計，不是強制的不變式 — 如果最後一擊出自隊友之手，戰報就會如實寫出來（33 個測試檔案）
- **[todak](https://github.com/euuuuuuan/todak-public)** — 住在你螢幕底部的桌面像素寵物（36 個測試檔案）
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)**（30）· **[ragtail](https://github.com/euuuuuuan/ragtail-public)**（29）· **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + 它的 **[Unity 移植版](https://github.com/euuuuuuan/driftfolk-unity-public)** — 移植版的重點就在於和原版的數值一致 · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — 指揮式自主的大逃殺，Unity。單獨列出，是因為這份快照是**驗證骨架，不是可遊玩的切片**：交付的部分是確定性模擬與其黃金檔測試工具，15 條編號浸泡測試斷言中還有 11 條尚未完成。

**AI 與代理工具**
- **[baton](https://github.com/euuuuuuan/baton-public)** — 代理平台：63 個工具的 MCP 介面、核准仲介、16 個編排器套件、90 個 Swift 原始檔
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — 唯讀推理測試工具，8 個 MCP 工具、0 相依套件
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — 兩個編碼代理之間、以閘門收尾的循環
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — 位於 fail-closed 授權閘門之後、本機零成本的素材生成
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — 生成 → 評審 → 組稿 → 發布，一旦「不確定」就把該次執行擱置，而不是照樣出貨

**資料、後端與領域工具**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — 多租戶資產管理，租戶邊界落在 PostgreSQL 裡：11 個遷移檔案中共 **82 條資料列層級安全（RLS）政策**，並有 6 個 pgTAP 測試檔案專門對著它們打 — 不是應用層過濾
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — 以評估閘門為重點的檢索
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — 代理 + 治理核心，跑在全合成資料的 CDP 上
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — 韓國金融／保險廣告文案的初篩 linter。內建的 5 套規則集全部在資料本身標示 `"verified": false`，而且報告缺了免責聲明就印不出來：它標記文案供人工審查，不做合規認證
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — 可以直接演奏樂器，也可以編寫音序；同一首歌，兩邊都能無損編輯（27 個測試檔案）
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — 雙語靜態網站，每個數字都聲明自己是量測值還是估計值 · [線上](https://euuuuuuan.pages.dev)

---

## 關於這些儲存庫

全部 27 個儲存庫都是經過淨化的快照：程式碼採 Apache-2.0 授權，素材由各儲存庫的 4 級
`CREDITS.md` 規範。遊戲是個人建置與垂直切片 — 可遊玩、有閘門把關、標示誠實，
並非商業發行。

**聯絡方式**：到這裡任何一個儲存庫開 issue 或 discussion 即可。
