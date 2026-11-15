# 報告 Catalog（index 分析）

來源：`dqa-report\Functional Report\` 下 17 個 share 的
`Functionality_Test_Report_List.xls`（index 檔）。

## 總覽
- 17 個 share（每個產品線/世代一個）
- **449 筆**報告記錄，2021-08 ~ 2025-04
- **272 個不同 DocNo**（一份 DocNo 可對應多 PN 版本）
- version 最大 32 版（單一 PN 的 EC 系列可長達 A0~A31）

## Share 清單（含 4U80、4U88、JBOD、SSG、PSG、ESG 等產品線）
17 個 share 各自一個 index；結構一致（DocNo｜PN｜Model｜Phase｜Rev｜Date｜報告人 等欄）。
**M1 catalog 挖掘的入口**：先全量讀 17 個 index → 建 PN×Phase×Rev 的完整矩陣 →
再對每份報告做結構分析（sub-step 詞彙抽取）。

## 檔案伺服器只讀政策
- `192.168.1.122` 對本專案**只讀**；index xls 不寫回
- D8 決策：pipeline 產出該行文字，使用者手貼（或另行授權）
