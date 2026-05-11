# Case: Phase 6 Origin — Why Belief Source-Trail Was Added

**Date:** 2026-05-12
**Phase highlighted:** Phase 6 (Belief Source-Trail) — the complement to fact source-tracing.

---

## The gap that prompted this phase

By v1.4, Source-Trail had Phases 0–5 reliably producing factually correct verifications. Phase 0.3 caught false premises. Phase 2 enforced T1 sourcing. Phase 5 (v1.4) required ACH. The methodology was rigorous on the *facts*.

While reviewing the Hualien 300 億 case (`cases/001-hualien-300-billion.md`), the user observed:

> 「我覺得有一個訴求沒有被解決：我們把事實拆解出來了，但是，以花蓮這則為例，是什麼導致他們這樣想的呢？」

(We've broken down the facts, but what made them believe this in the first place?)

Phases 0–5 were entirely about *what's true*. They had no slot for *why a different belief became plausible to the reader*. A factually correct verification published without this layer reads as condescension. The reader's natural rebuttal is:

> "If you're right and so many people believed the wrong thing, are they all stupid?"

The answer is almost always **no**. And the analyst's failure to address this question makes the verification rejectable even when the facts are correct.

---

## Why the absence of Phase 6 was dangerous

Source-Trail's stated goal is to make fact-checks **receivable**, not just correct. A correct-but-condescending output:

1. Gets shared less (no one wants to look down on people in their own network)
2. Gets emotionally rejected by readers who hold the false belief
3. Reinforces the "smug fact-check" pattern that has discredited mainstream fact-checking institutions globally
4. Treats false belief as a moral failing rather than a structural outcome — which is both ethically wrong and empirically wrong

The user's principle, stated in his own words:

> 「人們的情緒跟焦慮是真實的，要先被同理，才能夠看得下溯源的證據」

*Emotions and anxieties are real; they must be empathized with before someone can read the source trail.*

This is the same insight that distinguishes patient medical care from clinical correctness, good teaching from accurate lecturing, and durable journalism from "well, actually" pedantry. The fact alone is not the deliverable. The fact *plus the conditions under which the listener can absorb it* is the deliverable.

---

## What Phase 6 does

Phase 6 produces a **Structural Factor Analysis**: a written table identifying the conditions under which the false belief was a *natural near-guess* given the listener's actual information environment.

**Minimum requirements:**
- At least 3 factors (single-cause explanations are reductive and usually wrong)
- At least 2 distinct categories from: Cognitive, Political, Media, Propagation, Psychological
- An explicit empathy frame: one sentence stating that believing the false version was rational under these conditions

**Critically, Phase 6 is not:**
- A "both-sides" hedge
- A defense of the false belief
- An attribution of fault to a political tribe
- A substitute for the factual conclusion

It is a **separate, parallel layer** that runs alongside the factual verification. Phase 0–5 say "X is true, not Y." Phase 6 says "and here is why Y was the natural belief under the conditions."

---

## Applied to the Hualien 300 億 case

The verified facts (v1.4):
- The 300 億 in question is most likely the Matai'an dam special budget (Confidence ~75-80%, with 0403 reconstruction plan as plausible alternative at ~15-20%)
- Both budgets are central government, executed by central ministries
- The Hualien county magistrate has no disbursement authority over either

Without Phase 6, this verification asserts a county magistrate's innocence on this specific charge — which would read, to a reader holding the original belief, as defending a politician they distrust.

With Phase 6, the verification adds structural factors:

| 因素 | 性質 | 對本案的作用 |
|---|---|---|
| A. 預算分工的結構性知識赤字 | 認知 | 「中央特別預算 vs 縣府年度預算 vs 統籌分配款」這些分工概念對一般大眾不是常識，因此「花蓮拿到 300 億」很容易被理解成「縣府手上有 300 億」 |
| B.「花蓮王朝」敘事框架已建立 | 政治 | 傅崐萁/徐榛蔚多年累積的政治形象，給「花蓮 + 大筆中央資金」這個組合一個預設解讀腳本，新事件自動套進舊框架 |
| C. 新聞標題的主詞模糊 | 媒體 | 標題常用「花蓮拿到 300 億」這類模糊主詞，沒有清楚區分「給花蓮地區／給縣府／給中央在花蓮的計畫」 |
| D. 社群敘事的簡化偏好 | 傳播 | 「國王夫妻把 300 億花掉」比「中央特別預算分 6 年由 5 個部會執行」好傳播得多 |
| E. 事件記憶的混疊 | 認知 | 0403 地震款 285 億 + 堰塞湖款 300 億 + 縣府年度預算 401 億等，在記憶裡合成為「花蓮這幾年拿很多錢」 |
| F. 政治認同的歸因傾向 | 心理 | 立場光譜上的歸因偏誤——任何陣營的支持者，都更容易把負面事件歸因給對立陣營的政治人物 |

**同理框架：** 在這些條件聚合下，相信「國王夫妻把 300 億花掉了」是一種**理性的近似猜測**，不是愚蠢，也不是惡意——它只是把分工複雜的事實，用最熟悉的解讀腳本還原成一個能傳播的故事。

This shifts the verification's stance from "you were wrong" to "here's what's actually happening AND here are the conditions that made the simpler version sound right." The reader doesn't have to admit personal error to accept the correction.

---

## Design constraints

Phase 6 is dangerous if done poorly. Specifically:

### Anti-pattern 1: Politicizing the analysis

If all six factors lean one political direction, the analyst is bringing their own frame. Stress-test by trying to write the same factors with the political valence reversed. If the resulting analysis still rings true, the structural account is robust; if it collapses, the original was politically loaded.

### Anti-pattern 2: Veiled contempt

"People can't tell the difference between special budget and county budget" is contempt dressed as analysis. Phase 6 phrasing must describe a *condition* (knowledge structure of the average reader), not a *deficit* (this group is dumb).

### Anti-pattern 3: Single-attribution

"People believe this because of media bias" or "because of partisan loyalty" reduces a multi-factor phenomenon to one cause. False beliefs spread through compounding factors. Phase 6 must enumerate multiple.

### Anti-pattern 4: Both-sidesing

Phase 6 is not "well, both sides have a point." It is specific structural analysis of *the conditions under which one specific false belief became plausible*. Naming the conditions does not require diluting the factual verdict.

---

## Where Phase 6 fits in the disclosure stack

```
Reader's progression through the output:

1. Conclusion (what's actually true)
   ↓
2. Evidence (why it's true — T1 sources, ACH from Phase 5)
   ↓
3. Phase 6 — Belief Source-Trail (why the other version was plausible)
   ↓
4. Verification metadata (sources, falsifiability, search log)
```

Phase 6 sits between the factual case and the metadata. It is the bridge from "here is the correct answer" to "here is why this matters to you."

---

## Why this is the most important phase added so far

v1.0–v1.4 were about preventing analyst error. Each version closed a failure mode in *how the analyst handles facts*.

v1.5 is about something different: **preventing reader rejection of correct analysis**. It is the first phase that takes seriously the social reality that correctness is necessary but not sufficient for a fact-check to do its work in the world.

This is also the first phase whose required artifact is not a check on the *facts* but a check on the *audience*. Source-Trail v1.5 is no longer purely epistemological — it has an explicit communicative dimension.

The user's framing — that emotions and anxieties are real and must be empathized with before evidence can be heard — is the operational principle Source-Trail had been missing.

---

## Lineage

- Trigger: User question during review of `cases/001-hualien-300-billion.md` on 2026-05-12.
- Failure mode: Fact-checks without empathy layer get rejected even when correct.
- Fix: SKILL.md Phase 6 — Belief Source-Trail with required Structural Factor Analysis.
- First application: `cases/001-hualien-300-billion.md` updated to include the Phase 6 table for the Hualien 300 億 case.
