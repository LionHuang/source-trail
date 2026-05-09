# Case: Faker — Strongest Korean Esports Player?

**Date:** 2026-05-08
**Question asked:** 「韓國的電競明星 Faker，現在還是韓國最強的選手嗎」
**Phase highlighted:** Phase 4 (Peer Review) — Gemini's critique is double-edged: methodologically sharp AND fabricates its own specifics.

---

## What went wrong (without the skill)

A new session, asked this question, produced (paraphrased):

> "Based on my August 2025 knowledge cutoff: Faker won the 2024 World Championship and continued competing in 2025 LCK season. As for whether he's still the strongest in 2026 — that's beyond my knowledge cutoff. I recommend checking Naver Sports or OP.GG for current data."

This is the Iron Law violation in the wild. The model:
1. Answered partially from training data
2. Disclaimed the present-tense part
3. Pushed the verification work onto the user

## How the skill fixes this

### Phase 0 — Trigger
"現在還是" (current state), "最強" (superlative). Multiple triggers fire.

### Phase 0.5 — Pre-registration
> "Searching: 'Faker T1 LCK 2026 Spring performance', 'Faker 2026 LCK MVP best mid laner Korea'. Target T2: Liquipedia, Leaguepedia. T1: T1 official + LCK official."

### Phase 1 — SKIPPED
Scope is clear (one player, established sources). Phase 1 not needed.

### Phase 2 — Verification (the Iron Law executes)
Two WebSearches → multiple T2 sources retrieved:
- Inquirer Esports: "T1, Faker Open 2026 with a perfect LCK Cup Group Stage"
- Liquipedia, Leaguepedia: current rosters, season stats
- Sheep Esports: T1 won 2026 LCK Season Opening, Faker MVP vs HLE

### Phase 3 — Disclosure Output

**【已驗證】Verified (T1/T2):**
- Faker (李相赫) is on T1's 2026 roster, active mid-laner
- T1 won 2026 LCK Cup with perfect group stage opening
- T1 won 2026 LCK Season Opening, Faker named MVP vs HLE
- 2025 World Championship made him historical record holder for most titles

**【主觀判斷】Subjective:**
- "Strongest right now" is not a settled claim. Korean discourse differentiates:
  - Laning / individual carry → Chovy (Gen.G) often ranked higher
  - International / clutch performance → Faker
  - Zeka, Showmaker also remain in top tier

**【刻意不採信】Speculation flagged:**
- Search snippet auto-summary mentioned "Faker won 2026 MSI MVP" — but as of 2026-05-08, MSI 2026 has not concluded. Search-engine summary hallucination. Excluded.

**Reproducibility Log:**
- Searches run, URLs fetched, dates noted

**Falsifiability:**
- Wrong if Faker has been benched since latest source date.

## Phase 4 — Peer Review (where it gets interesting)

User opted to send the answer to Gemini for peer review.

```bash
echo "你是電競領域的同儕審查者。我剛回答了一個問題：『Faker 現在還是韓國最強的選手嗎？』我的結論是：[answer]。請挑出盲點。" | gemini
```

Gemini returned a multi-point critique:

### Methodologically valid critique (absorbed)
- ✓ "KDA is a result indicator, not an ability indicator. Should look at CSD@15, XPD@15, GD@15 for individual laning strength."
- ✓ "'Strongest' should be decomposed into: laning pressure, teamfight efficiency, strategic command, version adaptability — say which dimension Faker is #1 in."
- ✓ "T1 is a stable 5-player roster. Faker's stability gets a boost from Keria's roaming and Oner's jungle control. Distinguish 'best system core' from 'best individual.'"
- ✓ "Narrative framework is stale. The 'regular season Chovy / playoff Faker' framing is a 2023-2025 trope; 2026 has new mid-laner emergence to consider."

### Specific claims fabricated by Gemini (filtered out)
- ✗ "2026 LCK Cup uses Fearless Draft" — Gemini gave no source
- ✗ "Showmaker had a decline trend in late 2025" — no data to support, just narrative
- ✗ "Current 16.x version" — Gemini explicitly labeled it as "假設" (assumed)
- ✗ "New mid-laner emergence" — direction may be valid, but no specific player named

## The meta-irony

Gemini's core message was: **"Your answer is too shallow, relies on reputation over specific data."**

But Gemini itself was simultaneously:
- Inventing version numbers
- Inventing tournament formats
- Inventing player decline trends
- Speaking in generalities to sound authoritative

This is exactly the user's concern about LLM hallucination, demonstrated by Gemini critiquing Claude. **Gemini's appearance of precision is performative**: it sounds like it has insider knowledge, but the specifics often fail to bottom out.

## Lesson

Gemini-as-peer-reviewer has real value in **methodological challenges**: it forces the answerer to defend the framework, not just the conclusion. But:

- Methodology critique = signal
- New "facts" Gemini introduces = noise (unless re-verified)

The skill's filtering rule:

> Absorb the methodological critique. Treat any new specific claims as unverified — must re-verify in Phase 2 if used.

Without this filter, the answerer would absorb both signal and noise, propagating Gemini's hallucinations as "things the peer reviewer pointed out."

## What this case teaches

1. **Peer review is asymmetric.** Reviewers can point at gaps without filling them. Gemini's "your data is shallow" critique is valid even if Gemini's own substitute data is invented.
2. **Don't conflate Gemini's roles.** Phase 1 (scout) and Phase 4 (peer reviewer) are different uses with different output treatments.
3. **The skill doesn't make Gemini reliable — it makes Claude better at using Gemini.** Filtering is the active step.
