# catalog/（測項 catalog — M1 建立）

此目錄放測項 catalog 的資料檔（M1 milestone 產出）。

## 預定內容
- `catalog.json`（或 .csv）：sub-step 級測項詞彙
  - 欄：`id｜chapter｜item｜sub_step｜procedure｜criteria｜product_lines[]｜default_na`
- `source/`：挖掘用的歷史報告本地快取（必要原件，**不含凭证**）
- `mining_report.md`：挖掘方法、覆蓋率、衝突與例外清單

## 來源
- 主：17 個 share 的 `Functionality_Test_Report_List.xls`（449 筆）→ 逐份結構分析
- 輔：`dqa private\_Test_Report(Functionality)_Template\AIC Test Report_Template(直式)_2023_DQA_0828.xlsx`
  的 SSG sheet（716 行 superset，含 procedure/criteria 原文）
- 方法：python-docx body-order iteration＋merged-cell 去重；xlrd 讀 .xls

## 狀態
⬜ 未開始（等 M0 的 D2 拍板）。
