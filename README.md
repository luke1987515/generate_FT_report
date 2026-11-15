# generate_FT_report（AIC DQA FT 報告 pipeline）

> **TL;DR for handoff (agents & humans):** Tooling project that generates AIC DQA
> **Functionality Test (FT) reports** (docx) from a per-EC test-result workbook,
> mirroring the proven `generate_RT_report` pipeline (sibling repo). State as of
> 2026-11-14: **design phase** — ground-truth analysis done (templates, 449-report
> catalog, DocNo/filename conventions, real EC report anatomy), all design decisions
> **D1–D9 open** (see GitHub issues, milestone M0). No generator code yet.
> Knowledge lives in: this README + `docs/` (durable facts), the `aic-rt-report`
> Hermes skill (procedural), and the issue tracker (backlog).

## 背景

AIC DQA 的功能性測試報告（FT report）目前半手工產出：

1. 測試結果記錄在巨幅 Excel（`Test_Report(Functionality)_Template_20230828.xlsx`，
   SSG sheet 716 行 34 欄，每產品線一個 sheet：SSG/PSG/ESG）
2. 報告 docx 手建：封面、EC 沿革（Revision History）、DUT 配置表、測項表
   （9~146 行視 EC 範圍）、RAID/HBA、Summary
3. 同一測項（HDD Hot-swap、sg_ses page 0x2/0x7 等）在 449 份歷史報告中反覆手打
4. DocNo、檔名、version（A0→A16 遞增）、報告 index 全手工

目標：**測試人員每 EC 只填 3 欄（結果 / 觀察值 / 證據），其餘全部預載，
報告 docx 由 pipeline 產出。**

## 現況（2026-11-14）

- ✅ 地面真值分析完成（詳 `docs/`）
- ⬜ 設計決策 D1–D9 待拍板（GitHub issues，milestone M0）
- ⬜ 測項 catalog 挖掘（M1）
- ⬜ pipeline v1（M2）
- ⬜ 第一份真實 EC 報告對齊（M3）
- ⬜ 每週新報告對帳 cron（M4）

## 環境與存取

| 項目 | 值 |
|---|---|
| 公司檔案伺服器 | `192.168.1.122`（SMB，只讀政策：**絕不新增/修改/刪除**，除非使用者明確逐次同意） |
| 報告存放 | `\\192.168.1.122\D1000_SDRD\<DQA 部>\dqa private\`（工作區）與 `...\dqa-report\`（蓋章最終檔） |
| 工作機器 | Windows Server 2022，Python 於 `C:\Users\Administrator\AppData\Local\Python\bin\python.exe` |
| 凭证 | **不在本 repo**（公開倉庫）。見 Hermes skill `aic-rt-report` 與 session memory |

## 已確定的事實（摘要，細節見 docs/）

- **DocNo 規則**：`{F|C}{MMDD}{Y|B}{Seq}`，MMDD=報告日期（1 月補零、11/12 月不補）、
  Y/B 由月別推導（1-8 月→Y，9-12 月→B）、F=Functional/C=Compatibility。
  詳 `docs/doc-no-convention.md`
- **檔名規則**（449 份中 440 份符合）：`{DocNo}-{PN}_{model} [with BMC] {line} JBOD-{Phase}-{Rev}.pdf`
- **真實 EC 報告是全表格化 docx**：0 個 Heading 段落、無 token；
  固定骨架（logo/封面/簽核/RAID 114x5/HBA 22x5）＋動態表
  （T4 EC 沿革 55x24、T7 測項 9x3~146x3、T10 Summary 95x3）。
  RAID/HBA 章節與 2023 模板尺寸相同＝直接沿用。詳 `docs/real-reports-anatomy.md`
- **測項 catalog 資料源**：index `Functionality_Test_Report_List.xls`（17 個 share、
  449 筆、2021-08~2025-04、272 個不同 DocNo）。詳 `docs/report-catalog.md`
- **2023 模板結構**：4 個模板（直式×2 2021/2023 + 橫式 2016 + Function 2016 2U4N）
  ＋ RT 模板 + 8 個 RT xls 輸入模板。詳 `docs/ft-templates-2023.md`

## 設計決策 D1–D9

| # | 決策 | 建議 | Issue |
|---|---|---|---|
| D1 | 記錄介面：Excel / Web UI / Hybrid | ~~Excel~~ ✅ **定案：Excel（v1）**，GUI 路徑保留（[#11](https://github.com/luke1987515/generate_FT_report/issues/11) M5） | [#1](https://github.com/luke1987515/generate_FT_report/issues/1)（已關） |
| D2 | 測項 catalog 來源：挖 449 份 vs 手工 | ~~挖掘＋審訂~~ ✅ **定案：挖掘（2023 xlsx superset＋449 PDF）＋使用者審訂** | [#2](https://github.com/luke1987515/generate_FT_report/issues/2)（已關） |
| D3 | items 表 layout：單 sheet＋章節欄 vs 一章一 sheet | ~~單 sheet＋章節欄~~ ✅ **定案：單 sheet＋章節欄**（同現行 2023 xlsx 習慣） | [#3](https://github.com/luke1987515/generate_FT_report/issues/3)（已關） |
| D4 | Fail 是否強制填觀察值＋證據 | ~~Fail 強制觀察值~~ ✅ **定案：Fail 強制觀察值，證據選填**（產出前驗證） | [#4](https://github.com/luke1987515/generate_FT_report/issues/4)（已關） |
| D5 | EC 沿革（T4）格式：半結構化行 vs 原始文字貼上 | ~~半結構化行~~ ✅ **定案：半結構化行**（`類型｜檔名｜版本｜日期｜changelog`，可跨 EC 累積） | [#5](https://github.com/luke1987515/generate_FT_report/issues/5)（已關） |
| D6 | version（A17）自動建議＝index 該 PN 最大值＋1 | ~~自動預填＋可覆寫~~ ✅ **定案：自動預填＋可覆寫**（DocNo 流水號同邏輯） | [#6](https://github.com/luke1987515/generate_FT_report/issues/6)（已關） |
| D7 | base 模板：tokenize 某份 2026 真實報告 vs 改 2023 模板 | ~~tokenize 2026 真實報告~~ ✅ **定案：base＝`FC260918A16...EC-A16.docx`**（章節最全；M2 tokenize 收進 `templates/`） | [#7](https://github.com/luke1987515/generate_FT_report/issues/7)（已關） |
| D8 | index xls 更新：工具輸出該行 vs 授權寫入 | 輸出該行，使用者手貼 | [#8](https://github.com/luke1987515/generate_FT_report/issues/8) |
| D9 | 產出目標＋PDF：docx 放專案資料夾；Word COM 自動轉 PDF | docx 為主，PDF 可選 | [#9](https://github.com/luke1987515/generate_FT_report/issues/9) |

Umbrella：[#10 M0: 閉 D1–D9](https://github.com/luke1987515/generate_FT_report/issues/10)

## 資料模型（討論中，隨 D1–D9 定案更新）

**A. 測項 catalog（建一次，repo 內資料檔，長期維護）**
- 挖 449 份歷史報告 → sub-step 級詞彙＋章節歸屬＋產品線適用性＋procedure/criteria
- 每筆：`ID｜章節｜測項｜sub-step｜procedure｜criteria｜適用線｜預設 N/A?`
- 這是「模板會漂移」問題的根本解：catalog 是單一來源，docx/xlsx 模板只是渲染結果

**B. 單次執行 workbook（每 EC 一份，single-Excel 原則，同 RT pipeline）**
- `cover`：DocNo 自動算、Model/PN、標題、頁數（產出後回填）
- `EC info`：FW/MFG 版本表（檔名｜版本｜日期｜changelog）→ 渲染 T4
- `config`：Item｜Vender/Model｜Detail 塊
- `items`：主記錄表——依產品線從 catalog 預載，`本次適用` checkbox 決定章節去留＋重編號；
  右側 3 欄：結果（Pass/Fail/N/A 下拉）｜觀察值（Fail 必填）｜證據
- `summary`：自動彙整

**產出流程**：固定骨架（tokenized base，run-level 置換）＋動態表（Excel 逐行生成、
prototype row 複製）＋章節去留（有適用 row 才出章、自動重編號）＋檔名由 cover 算出。

## Roadmap

- **M0** 閉 D1–D9（設計定案，寫回本 README）
- **M1** 測項 catalog 挖掘：index 449 份 → 本地快取 → 結構分析 → catalog v1 → 使用者審訂
- **M2** pipeline v1：workbook → docx（固定骨架＋動態表）
- **M3** 第一份真實 EC 報告產出＋與手工作業對齊（差異→issue→修→commit→skill 更新）
- **M4** 每週 cron：掃 `dqa-report` 新最終報告 → 結構分析 → 對 catalog → 開 issue → 摘要推 Telegram
- **M5** GUI 前端（不阻塞 M1–M4）：v1.x Tkinter 單窗口（[#11](https://github.com/luke1987515/generate_FT_report/issues/11)）→ v2 Web

## 接手指南（human / agent）

1. **先讀**：本 README → `docs/` 各檔 → GitHub issues（M0 的 D1–D9 是當前阻斷點）
2. **環境**：Windows＋Python（路徑見上）；SMB 存取方式與凭证在 Hermes skill
   `aic-rt-report`（skill_view 載入）——**repo 內故意不放凭证**
3. **分析腳本**：結構分析模式＝python-docx body-order iteration＋merged-cell 去重
   （pypdf 讀 PDF；xlrd 讀 .xls）
4. **改動紀律**：
   - 每次 pipeline 改進＝commit；catalog 改動＝資料檔 diff
   - 新測項/結構漂移→開 issue
   - 定案後同步更新：本 README 的 D 表＋Hermes skill
5. **紅線**：公司檔案伺服器對本專案只讀；任何寫入（含 index xls）需使用者明確逐次同意

## 相關資源

- 姊妹 repo：`luke1987515/generate_RT_report`（RT 報告 pipeline，本專案的方法論來源；
  同機器、同 Excel→docx 模式、同 run-level token 置換）
- Hermes skill：`aic-rt-report`（環境、路徑、SMB 存取、模板與 DocNo 規則、踩坑記錄）
- 本專案分析用本地快取：Hermes scratch 目錄的 `smb_2023_tpl/`（2023 模板全套＋
  2 份真實 EC 報告＋3 份工作 docx 原件）——scratch 會清，**重要素材應入本 repo**（M1 時把
  必要的 docx/xlsx 原件放 `templates/` 與 `catalog/`，凭证仍不放）
