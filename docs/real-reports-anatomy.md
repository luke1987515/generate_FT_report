# 真實 EC 報告解剖（工作 docx 原件）＋與模板的差異

來源：`dqa private\Project_data\<project>\` 下的工作區（每版本 docx＋pdf 成對），
分析樣本：J4078-02-04X EC A16（2026-09-18，38pp）、J4108-02-04X EC A4（2026-09-29）、
J4078-02-04X EC A9（2025-03-21）。

## 工作區結構
- 專案資料夾：`Project_data\J4078-02-04X\`、`J4108-02-04X\` 等
- 每版本：`{DocNo}-{PN}_{model} [with BMC] {line} JBOD-{Phase}-{Rev}.docx` ＋同名 `.pdf`
- 2026 版開始去掉 "with BMC"（`FC260529A15...JBOD-EC-A15`）
- 子資料夾可能含 performance 測試資料（例：`EC-A3_2026-03-09_..._performance`）
- **蓋主管章的最終 PDF** 另存於 `dqa-report\Functional Report\SSG\`（工作版只蓋 Luke 的章）

## docx 全表格化（3 份樣本共同特徵）
- **0 個 Heading 樣式段落**、無 token；全文只有 ~30 個段落（封面標題/說明）
- 章節＝表格，pipeline 要建的是表格不是標題

| 表 | 內容 | J4078 A16 (2026) | J4078 A9 (2025) |
|---|---|---|---|
| T1 | logo | 1x2 | 1x2 |
| T2 | 封面資訊（Date/Doc. Number/Doc. Version/Number of Pages/Test Report Perform By） | 5x3 | 5x3 |
| T3 | 簽核（DQA / 'YY.MM.DD / Name） | 2x3 | 2x3 |
| T4 | **Revision History＝EC 前置內容**（EC 變更清單＋FW/MFG 版本沿革：.ima/.tar 檔名、逐版 changelog） | 55x24 | 41x11 |
| T5 | 2.2 DUT Main Hardware Configuration | 11x4 | 13x4 |
| T6 | **SKU 表**（`SKU：Dual EXP (SAS4 Hub, ...)`）— **2023 模板沒有** | 6x2 | — |
| T7 | 3. JBOD Functional Test（3 欄：item/step｜procedure/criteria｜Result） | **146x3** | **9x3** |
| T8 | 4. RAID Card Test | 114x5（＝2023 模板尺寸） | — |
| T9 | 5. HBA Card Test | 22x5（＝2023 模板尺寸） | 19x4 |
| T10 | 6. Summary（Item｜Descriptions｜Result，95 行） | 95x3 | 72x5（5. BMC Functional Test 取代 RAID/Summary） |

## 關鍵結論
1. **RAID/HBA 章節直接沿用 2023 模板**（尺寸完全一致）——固定骨架
2. **每次 EC 長出來的是 T4、T7、T10**——動態表
3. **章節集合隨 EC 範圍變化**：小 EC 可能只有 3.x＋Summary；碰 BMC 才出
   BMC Web UI＋Redfish 章；所以章節號在報告間到處跳（3.x/4.x/5.x 不固定）
4. **EC-only 3.x 測項**（模板 313 行 superset 之外的）：Temperature Sensor、
   JBOD Remote、diag_drive_led、enclosure addr、Check_wide_port on/off/standby、
   serial number and enclosure number、sensor、AT Switch test（power cord/front SW）、
   Customer requirement
5. 測項粒度＝**sub-step 級**（例 3.13 HDD Hot-swap 分 plug-in/remove 兩行）
6. 頁尾：`AIC Inc. REV Axx | N # of M`；封面簽核格式 `DQA / '26.09.18 / Luke.Chen`
7. 真實報告的 EC 前置內容（16pp 的 FW/MFG 沿革）全住在 T4 大表裡——
   T4 會隨 EC 數量長大（41x11→55x24→58x28）

## 與 2023 模板的差異（使用者指示關注點）
- 新增 SKU 表（T6）
- EC 前置內容（T4 的 EC 清單＋版本沿革）在 2023 模板的 Revision 章只有 2 行佔位
- 章節編號動態重排（模板是固定 2.1~2.8）
- 2026 命名去 "with BMC"
- 2023 模板的 token 只有 3 個（見 ft-templates-2023.md）→ D7 要決定 base 策略
