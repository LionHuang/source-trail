# Case: TSMC Quarterly Revenue

**Date:** 2026-05-08
**Question asked:** 「2026 年台積電最新一季營收是多少」
**Phase highlighted:** When to skip Phase 1 — T1 source is obvious, scout phase adds no value.

---

## What went wrong (early skill version)

In an earlier draft of the skill (before the Iron Law was added), a new session encountering this question produced:

> "未驗證 — 知識截止限制
>
> 我的知識截止日期是 2025 年 8 月，無法提供 2026 年台積電的財報數據。建議直接查詢以下 T1 來源：
> - 台積電官網投資人關係：ir.tsmc.com
> - 台灣證券交易所
> - Bloomberg / Reuters"

**This was a failure.** The skill's structure (T1 classification, disclosure format) was triggered correctly, but the model defaulted to "knowledge cutoff → recommend you check elsewhere." It listed where to look instead of looking.

The failure mode: knowing where to verify ≠ verifying. The skill listed T1 sources accurately but stopped short of actually fetching them.

## How the skill fixes this

The Iron Law was added explicitly:

> **Knowledge cutoff is a tool-call signal, not a give-up clause.**

After this fix, a subsequent test produced correct behavior:

### Phase 0 — Trigger
"2026 年", "最新", "營收" → multiple triggers. Skill activates.

### Phase 0.5 — Pre-registration
> "I will fetch TSMC investor relations directly. Target T1: ir.tsmc.com/investor/quarterly-results. Scope is clear (TSMC is the only entity in question, official IR is the canonical source) → skip Phase 1."

### Phase 1 — SKIPPED
Scope is clear, T1 source is obvious. No reconnaissance needed.

### Phase 2 — Verification
Direct WebSearch + WebFetch to TSMC IR page and corroborating financial press.

### Phase 3 — Disclosure Output

**【已驗證】Verified (T1 + T2):**
- Q1 2026 net revenue: NT$1.13 trillion (TSMC IR 2026-04-XX, retrieved 2026-05-08)
- YoY growth: +35%
- Net profit: $18.16B USD, +58% YoY
- Gross margin: 66.2%

**Reproducibility Log:**
- URL fetched: ir.tsmc.com/investor/...
- Corroborated by: financial press summaries citing the same earnings call

**Falsifiability:**
- Answer would be wrong if TSMC restates earnings before next quarterly filing.

## Lesson

The skill's value here is not in elaborate scouting — it's in **forcing the tool-call** when the model would otherwise punt to "go check yourself."

Phase 1 was correctly skipped because:
- One entity in question (TSMC)
- One canonical T1 source (IR site)
- No ambiguity about what "the answer" looks like

Compare to the baseball case where Phase 1 was decisive: there the entity set was unknown.

## What this case teaches

1. **Phase 1 is a cost, not a default.** Don't run it for clean queries.
2. **The Iron Law is the skill's most important rule.** Without it, Phase 2 silently degrades into "recommend the user search."
3. **Pre-registration in Phase 0.5 is short for clean queries.** "Fetch X from Y because Z" is enough — don't ceremonialize.
