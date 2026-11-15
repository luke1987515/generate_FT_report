# 2023 模板結構（dqa private\_Test_Report(Functionality)_Template）

來源：`\\192.168.1.122\D1000_SDRD\<DQA 部>\dqa private\_Test_Report(Functionality)_Template`
（16 個項目；本分析覆蓋 4 個 docx 模板＋RT 模板＋8 個 RT xls 輸入模板）。

## FT docx 模板

### A. `AIC Test Report_Template(直式)_2023_DQA_0828.xlsx` — **主數據模板**
Excel，8 個 sheet（每產品線一個：SSG 716 行×34 欄、PSG/ESG 等＋配置 sheet）。
章節：3. Config（Test Config＋DUT Main HW Config）→ 4. JBOD Functional（SSG ~313 行）
→ 5. RAID Card Test → 6. HBA Card Test → 7. BMC Web UI Test → 8. Power Consumption Test。
34 欄寬含 Test Config（Model/SSD/HDD/RAID/BMC FW/BIOS FW/PSU 等）。
測項 procedure 內嵌實際指令（sg_ses --page=0x2/0x7、diskpart、fdisk、iometer burn-in、
sg_utils shutdown、enclosure addr、AT Switch test、Customer requirement）。
**這是測項 catalog 的主挖掘對象。**

### B. `AIC Test Report_Template(直式)_2021_DQA_0709.docx`（19pp）
Cover → TOC → Revision → 1 Test Environment → 2 Test Content
（2.1/2.2 DUT HW Config、2.3 SKU、2.4 JBOD Functional 72 行、
2.5 RAID 114 行、2.6 HBA 22 行、2.7 BMC Web UI 17 行＋4.1 Feature 11 項、
2.8 Redfish 8 項）→ 3 DUT Images → 4 Summary。

### C. `Test Report_Template(橫式)_2016_DQA_0519.docx`
2016 版直式前身，章節同 2021 但較短（2.4 36 行、2.5 RAID 55 行）。

### D. `Function Test Report_Template_2016_2U4N_1109.docx`
早期單產品線版本（2U4N），章節 2.3~2.7。

### 模板 token 狀態
2021 直式模板**只有 3 個 token**：`{PageNumber}`、`{Date}`、`{Name}`（footer 區）。
其餘全實值 → 若要當 generation base 需先 tokenize（D7 決策）。
模板章節編號與真實報告不同（真實報告 EC 版章節號依 EC 範圍重排）。

## RT docx 模板（同資料夾，姊妹 pipeline 用）
`Report_Template_2025_Reliability Test_0519.docx`（19pp）：
Cover → Revision → 1 Test Environment → 2 Test Item（2.1 JBOD 46 行、
2.2 HDD 17 行、2.3 SSD 17 行、2.4 System 2 行、2.5 RAID 2 行）
→ 3 Result（3.1 43 行、3.2 HDD 8 行、3.3 SSD 8 行、3.4 System 2 行、3.5 RAID 2 行）
→ 4 Power Consumption（4.1~4.4，4.4=56x2 功耗大表）→ 5 Summary（20 行）。
**RT 與 FT 的章節對齊（Test Item/Result/Power/Summary）是 generate_RT_report
pipeline 已驗證的模式，FT pipeline 沿用。**

## RT xls 輸入模板（8 個）
| 檔名 | sheets |
|---|---|
| Reliability_Test_Report_Template_20250519_SSG/PSG/ESG.xlsx | Cover, TOC, Test Item, Test Result, Power, Summary |
| Reliability_Test_Report_Template_20240326_SSG.xlsx | Cover, TOC, Test Item, RAID Card, JBOD, HDD, SSD, Power Consumption, Summary |
| Reliability_Test_Report_Template_20240410_ESG.xlsx | 同上 |
| Reliability_Test_Report_Template_20240412_PSG.xlsx | 同上 |

**Cover sheet（14 欄，RT pipeline 的輸入範本，FT workbook 直接沿用）：**
DocNo | Model Name | Test Type（下拉：Burn-in/Stress Test/MTBF/...）
| Test Location（下拉）| Test Time | Test Time (Month)
| DUT Number | DUT Image | RAID Card Number | HDD Number
| SSD Number | Total Number of Test | Report Time | Report Number (Page Number)
下拉值：Test Type={Burn-in, Stress Test, MTBF, MTBF & Stress Test, Stress,
Burn-in & Stress Test, Stress test, MTBF test, Burn in, Burn in & stress test,
Burn in test}；Test Location={AIC-2F, AIC-SMG}。

## 本地快取
Hermes scratch `smb_2023_tpl/`（會清，M1 時把必要原件收進 repo `templates/`）。
