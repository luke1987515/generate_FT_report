# 設計討論記錄（2026-11-14）

與使用者（AIC DQA 測試工程師）討論「測試人員視角的記錄介面＋報告產出」的結論與開題。

## 核心 UX 原則
**一次 EC 執行，測試人員只填 3 欄：**
- `結果`：Pass / Fail / N/A（下拉選單）
- `觀察值`：關鍵輸出（例：PHY rate 12.0G、locate LED 亮）——Fail 必填（D4）
- `證據`：截圖檔名/路徑（選填）

其餘全部預載（測項名稱、procedure、criteria、章節歸屬、配置表）。
痛點根源＝現在 716 行巨表要「找位置寫」。

## 記錄粒度
sub-step 級，不是測項級。
例：3.13 HDD Hot-swap 在報告裡是 plug-in / remove 兩行；
catalog 的筆＝sub-step，測項只是群組。

## 資料模型
### A. 測項 catalog（建一次，repo 內資料檔）
- 挖 449 份歷史報告 → sub-step 詞彙＋章節歸屬＋產品線適用性＋procedure/criteria
- 欄：`ID｜章節｜測項｜sub-step｜procedure｜criteria｜適用線｜預設 N/A?`
- 定位：catalog 是單一來源，docx/xlsx 模板只是渲染結果
  （解決「模板會漂移」——4 個 docx 模板＋8 個 RT xls 之間結構不一致）

### B. 單次執行 workbook（每 EC 一份，single-Excel 原則）
- `cover`：DocNo 自動算、Model/PN、標題、頁數（產出後回填）
- `EC info`：FW/MFG 版本表（檔名｜版本｜日期｜changelog 行）→ 渲染 T4
- `config`：Item｜Vender/Model｜Detail 塊（沿用 2023 xlsx 格式）
- `items`：主記錄表＝catalog 依產品線預載＋`本次適用` checkbox
  （決定章節去留＋自動重編號）＋右側 3 欄
- `summary`：自動彙整

## 報告產出
- 固定骨架（logo/封面/簽核/RAID 114x5/HBA 22x5）：
  tokenize 一份 2026 真實 docx 當 base（D7），run-level 置換
  （RT pipeline 已證的 generate_RT_report 方法）
- 動態表（T4 EC 沿革、T7 測項、T10 Summary）：Excel 逐行生成，
  prototype row 複製保留格式
- 章節去留：items 有適用 row 才出該章＋自動重編號
  （解釋 EC 報告章節號跳動）
- 檔名：由 cover 欄位算出（doc-no-convention.md），不手打
- PDF：Word COM 匯出（有 Word 的機器；`--no-com` 可跳過，D9）

## 開題（D1–D9）→ GitHub issues（milestone M0）
見 README 的 D 表。待使用者逐一拍板。

## 持續改進機制（使用者認可方向）
1. **repo**＝工具單一來源（code＋模板＋catalog＋README 定案記錄）
2. **Hermes skill `aic-rt-report`**＝程序記憶（每 session 自動帶走）
3. **GitHub issues**＝改進 backlog（每次產出報告的差異→issue→修→commit）
4. **cron（M4）**＝每週掃 `dqa-report` 新最終報告→結構對帳→自動開 issue→Telegram 摘要
5. 運作節奏：每次 EC 報告＝產出→目視檢查→差異入 issue→修→skill 更新；
   每週 cron 對帳。不需要另開 Hermes profile。
