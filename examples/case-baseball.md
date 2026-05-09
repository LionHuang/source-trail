# Case: 2026 MLB Taiwanese Players

**Date:** 2026-05-08
**Question asked:** 「今年目前為止在美國大聯盟出賽場次最多的台灣棒球員」
**Phase highlighted:** Phase 1 (Scope Reconnaissance) — when Gemini scouting saves you from solo WebSearch failure.

---

## What went wrong (without the skill)

Initial solo WebSearch by Claude returned mostly Wikipedia and Baseball Almanac results biased toward older / better-indexed content. Claude synthesized:

> "Tsung-Che Cheng is Taiwan's only current big leaguer in the 2026 season, having made his MLB debut with the Pirates in 2025."

**This was wrong.** Cheng had been demoted to Triple-A. Claude missed two active players entirely:
- 李灝宇 (Hao-Yu Lee) — Detroit Tigers, MLB debut 2026-04-17
- 鄧愷威 (Kai-Wei Teng) — Houston Astros pitcher

The failure mode: WebSearch ranks by general popularity, not freshness. Cheng's 2025 debut had more accumulated articles than Lee's recent debut, even though Lee was more relevant to the question.

## How the skill fixes this

### Phase 0 — Trigger
Question contains: "今年" (current state), "出賽場次最多" (comparative/superlative), "台灣棒球員" (specific identity). Multiple triggers fire. Skill activates.

### Phase 0.5 — Pre-registration
> "I will search for: 'Taiwanese MLB players 2026 season game logs'. Target T1: Baseball-Reference 2026 player listings. Expected candidates: I don't know who is currently active, so scope is unclear → Phase 1 needed."

### Phase 1 — Scope Reconnaissance (Gemini scout)
Scope is unclear → invoke Gemini:

```bash
echo "List all Taiwan-born players active in MLB during the 2026 season. Names only, with team and position." | gemini
```

Gemini output (candidates, not answer):
- 李灝宇 (Hao-Yu Lee) — Detroit Tigers, 3B
- 鄧愷威 (Kai-Wei Teng) — Houston Astros, P
- 鄭宗哲 (Tsung-Che Cheng) — currently in Triple-A, not active in MLB

### Phase 2 — Verification
For each candidate, fetch T1 source:

- **Lee:** Verified via mlb.com/player/hao-yu-lee-701678 (T1) and en.wikipedia.org/wiki/Hao-Yu_Lee citing original sources
- **Teng:** Verified via mlb.com/player/kai-wei-teng-678906 (T1)
- **Cheng:** Verified via Triple-A roster, not MLB roster (excluded)

Game count comparison via Baseball-Reference 2026 game logs.

### Phase 3 — Disclosure Output

**【已驗證】Verified:**
- Hao-Yu Lee made MLB debut 2026-04-17 vs Red Sox at Fenway (Tigers official + Yahoo Sports + Taipei Times, three independent sources)
- He is the most recently active Taiwan-born position player in MLB

**【中信心】Likely:**
- Lee has played ~15 games as of 2026-05-08 (single source: search-engine summary, not directly verified via game log fetch)

**【已知衝突】Disputed:**
- Game count for Teng: Gemini said 14, WebSearch said 10-11 appearances. Conflict not resolved.

**Reproducibility Log:**
- Searches: "Hao-Yu Lee 李灝宇 Detroit Tigers MLB debut 2026", "Kai-Wei Teng 鄧愷威 Astros 2026 stats"
- URLs fetched: mlb.com/player/hao-yu-lee-701678, en.wikipedia.org/wiki/Hao-Yu_Lee

**Falsifiability:**
- Wrong if (a) Teng has played significantly more games than Lee in the same period, (b) another active Taiwanese MLB player exists not flagged by Gemini.

## Lesson

Phase 1 (Gemini scouting) was decisive. Without it, Claude's WebSearch would have missed Lee entirely.

The trap that the skill prevents: treating Gemini's specific numbers (e.g., "Lee has 15 games, .171 BA, 1 HR, 5 RBI") as verified just because the *names* Gemini surfaced were verified. Structural facts being correct (player exists, plays for X) does not validate the specific stats Gemini also provided. Phase 3's estimative language forces the distinction.

## What this case teaches

1. **Scope reconnaissance is not optional when scope is genuinely unclear.** The skill's "skip Phase 1 if T1 is obvious" rule depends on knowing what to look for. Sometimes you don't.
2. **Gemini's search advantage is real but narrow.** It excels at finding *names of things that exist*. It still hallucinates specific data about those things.
3. **Verifying structural facts ≠ verifying specifics.** Lee being on the Tigers' roster is one claim. Lee having 15 games is a different claim that needs separate verification.
