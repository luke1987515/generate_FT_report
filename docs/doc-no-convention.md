# DocNo 與報告檔名規則

## DocNo：`{F|C}{MMDD}{Y|B}{Seq}`

| 段 | 規則 |
|---|---|
| 前綴 | F=Functional、C=Compatibility |
| MMDD | 報告日期：M/D 直書，**1 月補零**（`0106`）、**11/12 月不補**（`1106`、`1211`） |
| 3rd | **月別推導**：1-8 月→`Y`、9-12 月→`B` |
| 4th+ | 同日同類型流水（A1, A2, ...） |

例：`F0106Y1`（2025-01-06 Functional 第 1 份）、`C0106Y2`（同日 Compat 第 2 份）、
`FC250106Y1` 不合法——前綴與 F/C 二选一。
來源：`AIC RT Report Doc No 規則 2021.pdf`（9pp，含 2024 完整對照表）。

## 報告檔名（449 份中 440 份＝98% 符合）

```
{DocNo}-{PN}_{model} [with BMC] {line} JBOD-{Phase}-{Rev}.{ext}
```

- `{PN}_{model}` 例：`XJ1-40782-XX_XJ1-40782-XX`（PN 與 model 可能相同）
- `[with BMC]` 2026 版開始省略
- `{line}`：SSG / PSG / ESG
- `{Phase}`：EVT / DVT / PVT / EC
- `{Rev}`：EVT/DVT/PVT 用 `A0/A1/...`；EC 用 `A0~A16`（遞增）
- 真實樣本：
  - `FC250321A9-J4078-02-04X_XJ1-40782-XX with BMC SSG JBOD-EC-A9.pdf`
  - `FC260929A4-J4108-02-04X_XJ1-41082-XX with BMC SSG JBOD-EC-A4.pdf`
  - `FC260918A16-J4078-02-04X_XJ1-40782-XX SSG JBOD-EC-A16.pdf`（2026 去 with BMC）

## Version 遞增
- 同一 PN＋Phase 的 version 依序 A0→A1→...（index 中最大 32 版）
- D6 決策：pipeline 自動建議＝index 中該 PN 最大值＋1，使用者可覆寫
