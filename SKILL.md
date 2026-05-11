---
name: source-trail
version: 1.3.0
description: Use when answering factual questions involving specific numbers, dates, comparative claims (most/best/first), current states ("now", "latest", "still"), attribution (who/when/where), or any information that may have changed since training. SKIP for code logic, concept explanations, opinions, hypotheticals, creative tasks.
license: MIT
---

# Source-Trail — 溯源查證框架

> A multi-disciplinary verification framework synthesized from journalism, research ethics, intelligence tradecraft, and evidentiary standards. Forces explicit source classification, disclosure of confidence levels, and adversarial self-checking on factual claims.
>
> 整合新聞工作、研究倫理、情報分析、法庭證據四個領域的查證方法論，強制對事實宣稱做來源分級、信心揭露與對抗性自檢。

---

## Overview

When answering factual questions, every specific claim must be traceable to a primary or authoritative secondary source (T1/T2). LLM consensus is not verification. Disclose what is verified, what is unverified, and what conflicts.

事實宣稱必須上溯到 T1/T2 來源。LLM 之間的同意不算驗證。揭露已驗證、未驗證、衝突三類資訊。

---

## When to Use / When NOT to Use

### TRIGGER on these question types（觸發類型）

1. **Time-sensitive state（時間敏感狀態）** — "now", "latest", "currently", "still", "recent", anything beyond knowledge cutoff
2. **Specific numerical claims（具體數值宣稱）** — statistics, prices, populations, distances, revenues
3. **Dates / chronology（日期 / 時序）** — when X happened, release dates, founding dates
4. **Comparative / superlative（比較級 / 排名）** — most, top N, highest, lowest, first, best
5. **Identity / attribution（身分歸屬）** — who founded, who owns, who wrote, who currently leads
6. **Existence / status（存在性 / 狀態）** — is X still operating, is Y alive, does Z support feature W
7. **News / events（新聞 / 事件）** — recent happenings, ongoing situations, breaking news
8. **Technical specs / docs（技術規格 / 文件）** — library versions, API behavior, protocol details
9. **Regulatory / legal facts（法規 / 政策）** — laws, compliance requirements, jurisdiction-specific rules
10. **Geopolitical / changing geographic facts（地理政治變動）** — leadership, currencies, borders

### SKIP for these（跳過類型）

1. Code logic discussion 程式邏輯討論
2. Concept explanations (OAuth, REST) 概念定義
3. Opinion-seeking ("which is better") 意見徵詢
4. Hypothetical scenarios 假設情境
5. Creative tasks (naming, copywriting) 創作任務
6. Pure mathematics / logical reasoning 純數學推理
7. Stable historical consensus (Tokyo is in Japan) 穩定歷史共識
8. User's own data / context 使用者自身資料
9. Explaining user's own code 解釋使用者程式
10. Subjective evaluations 主觀評價

### Edge cases（邊界處理）

- **Mixed questions** — split: explain concept (skip) + give current example (trigger only this part)
- **"Best practices"** — opinion if abstract; trigger if citing specific cases/numbers
- **"I think X is true, verify?"** — always trigger
- **Questions about Claude itself** — usually skip; trigger if claiming current capabilities

---

## Stakes Assessment（風險等級判定）

Different questions need different rigor. Before running phases, classify the question:

| Stakes Level | Definition | Examples |
|--------------|------------|----------|
| **Low（低風險）** | Pure information lookup, no decisions involved, answer space is bounded | "List Taiwanese MLB players", "What's TSMC Q1 revenue" |
| **Medium（中風險）** | Comparative / evaluative claims, multiple plausible answers, opinion-adjacent | "Is X still the strongest", "Which library is most popular" |
| **High（高風險）** | Decision support, harm if wrong, contested or politicized | "Should I take this medication", "Is X investment safe", "Which legal interpretation applies" |

### Topic-Based Override（主題硬性歸類）

**The following topics are ALWAYS High stakes, regardless of how the question is framed.** Do not downgrade based on phrasing like "general info", "just curious", "in general".

| Topic Domain | Why always High | Override applies even if phrased as |
|--------------|-----------------|-------------------------------------|
| **Medical / Health（醫療 / 健康）** | Any reader may self-medicate based on the answer | "side effects of X drug", "what does X disease do", "is X treatment safe" |
| **Financial / Investment（金融 / 投資）** | Misinformation can cause direct monetary loss | "Is X stock good", "should I buy Y", "general info on Z fund" |
| **Legal / Compliance（法律 / 法規）** | Wrong legal info has compliance / liability consequences | "Is X legal", "what does Y law say", "general info on Z regulation" |
| **Safety-critical（安全關鍵）** | Wrong info can cause physical harm | Chemicals, electrical, structural, food safety, child safety |

**Why "general framing" doesn't lower the stakes:**
- Information itself is high-risk regardless of who asks
- Asker can hand the answer to someone who will act on it
- The skill cannot verify the asker's actual context
- Default to caution; cost of over-rigor << cost of under-rigor

**Default rule (still applies for non-override topics):** If unclear, treat as one tier higher.

---

## Performance Expectations（時間預期）

Realistic time costs for users to set expectations:

| Stakes | Phases | Typical Time |
|--------|--------|--------------|
| Low | 0, 0.3, 0.5, 2, 3 | 25-45s |
| Medium | + 1?, 4 | 45-65s |
| High | All (0–5) | 65-95s with parallel; 1.5-3min sequential |

Phase 0.3 adds ~5s but often saves more time downstream by preventing wasted searches on a wrongly-framed question.

Sequential execution can double the time. **Always parallelize independent fetches.** See parallelization rules in Phase 2 and Phase 5.

For users requesting speed: they can say "fast" / "lite" / "skip Phase 5" — degrade gracefully by skipping conditional phases first (5 → 4 → 1), keeping Phase 0.3 + 2 + 3 as the irreducible minimum. **Phase 0.3 is not optional even in lite mode** — it's the cheapest way to avoid the most expensive failure.

---

## Pre-flight Announcement（執行前預告）

Before running phases, announce the plan. The announcement format depends on stakes:

### Low / Medium stakes — Announce + Execute（預告後立即執行）

Display the plan, then start running immediately. User can interrupt via their interface's natural mechanism (sending a new message works in any UI).

```
🔍 /source-trail activating
   Stakes: Medium
   Phases: 0.5 → 2 → 3 → 4
   Expected: 4-5 searches
```

Do NOT instruct specific interrupt mechanisms ("press Esc", "type skip") — these are UI-specific and won't work across Claude Code, Desktop, claude.ai web, mobile, or API usage.

### High stakes — Announce + Confirm（預告後等確認）

Pause for explicit user confirmation before running. High-stakes questions warrant the extra round-trip.

```
🔍 /source-trail — High stakes detected (medical/financial/legal/decision-support)
   Plan: All phases (0–5), including counter-evidence search (Phase 5)
   Expected: 6-8 searches, ~60s

⚠ This will take longer than usual. Recommended for accuracy on this question type.
   Reply to confirm, or request a lighter version if appropriate.
```

Wait for user response before proceeding.

### Why this asymmetry

- **Low/Medium:** Auto-trigger means the user already implicitly authorized verification by asking a factual question. Confirming each time creates friction without proportional benefit.
- **High:** Decision-support and harm-if-wrong questions deserve a deliberate pause. The cost of confirming is small relative to the cost of an unverified high-stakes answer.

---

## The Iron Law

> **Knowledge cutoff is a tool-call signal, not a give-up clause.**
>
> 知識截止不是放棄的出口，是觸發工具的訊號。

When you detect a time point past your training cutoff, the correct action is to **call WebSearch / WebFetch immediately** — not to disclaim and recommend the user check elsewhere.

Failure modes that violate this law:
- ❌ "My knowledge cutoff is X, please check Y website for current data."
- ❌ "I cannot verify 2026 information. Suggest checking official sources."
- ❌ Answering from training data, then adding "but for current info, see X."

Correct behavior:
- ✓ "My training data is from X. Searching now for current data..." → actually searches → reports findings

---

## Source Tier System (T1-T5)

All factual sources are classified before use. Factual claims must trace to T1 or T2.

所有來源使用前先分級。事實宣稱必須能上溯到 T1 或 T2。

| Tier | Type | Examples | Reliability |
|------|------|----------|-------------|
| **T1** | Primary / Original | Original databases, official documents, raw statistics, party-of-record statements | Highest |
| **T2** | Authoritative Secondary | Reuters, AP, official spokespersons, peer-reviewed journals | High |
| **T3** | Reported with citation | News articles citing primary sources | Medium |
| **T4** | Aggregated summary | Wikipedia, summary sites | Low |
| **T5** | LLM-synthesized | Claude, Gemini, GPT outputs | Not evidence |

### Independence rule（獨立性規則）

"Independent" means **cannot trace back to the same original source**.
- ❌ Reuters citing MLB.com + ESPN citing MLB.com = ONE source (both trace to MLB.com)
- ❌ Gemini agrees + Claude agrees = ONE source category (both T5)
- ✓ Baseball-Reference + MLB official site = TWO independent T1 sources

### Citation Chain Integrity（引用鏈完整性）

Don't trust "X reported that Y said Z." Read what Y actually said. Game-of-telephone effects compound at each retelling.

不信「X 報導 Y 說 Z」。要讀 Y 原始說了什麼。每一層轉述都會失真。

### Wire Service Detection（通訊社偵測）

Multiple outlets running the same wire copy ≠ multiple sources. Always check if outlets are running the same Reuters/AP/AFP wire.

多家媒體跑同一通訊社稿件 ≠ 多重來源。必須偵測是否同一原稿被轉發。

### Time Context Decay（時效衰減）

Always note publication date of sources. A 2024 statistic answering a 2026 question must be flagged.

來源發布日要標註。用 2024 數據回答 2026 問題必須警示。

---

## The Verification Workflow (Phase 0-5)

### Phase 0: Trigger Check（觸發判斷）
Is this a factual question matching the trigger criteria? If yes, continue. If no, exit skill.

### Phase 0.3: Key Assumptions Check（前提核查）— from intelligence tradecraft

**Before searching, audit the question itself.** A precisely-verified answer to a wrongly-framed question is still wrong. This phase adapts the CIA's Key Assumptions Check (Tradecraft Primer 2009, pp.7-8) — originally designed for intelligence analysts to surface hidden premises before analysis begins.

#### When to run

- **Always run** for any question that survives Phase 0 trigger check
- **Especially critical** when the question comes from a third party (news headline, user quoting someone else, social-media post) — these arrive pre-framed by someone else's assumptions

#### The four-step procedure（CIA KAC 四步法）

This is an **execution checklist, not a declaration**. Each step must produce a written artifact that goes into the verification trail. If a step has no written output, that step did not happen.

1. **Restate the analytic line**（寫下目前的分析路線）
   *Action:* Write, in one sentence, what the question seems to be asking.
   *Output artifact:* `"The question appears to be: ___"`

2. **List all premises, stated and unstated**（列出所有前提，含未明說的）
   *Action:* Run through the checklist below. For each row, **write the specific premise the current question carries**, even if blank.

   | Premise type | Question to ask | Anchor tradition |
   |---|---|---|
   | **Existence / Falsifiability** | Is the "fact" being asked about even verifiable in principle? Does it even exist as a single thing? | Popper, *Logic of Scientific Discovery* (1934) |
   | **Actor / Object unit** | Who/what is the subject? Is the unit of analysis specified? | (general analytic clarity) |
   | **Authority / Jurisdiction** | Who has the legal/institutional power to do the thing being asked about? Often confused with Actor. | (added 2026-05-11 — see `case-political-substitution.md`) |
   | **Time anchor** | What time point does "now/recent/current" refer to? | (general analytic clarity) |
   | **Metric / Measurement** | By what indicator is the comparative claim measured? | (general analytic clarity) |
   | **Embedded stance** | Does the question presuppose its own answer? | Maxwell, *Qualitative Research Design* (3ed) — "Pay attention to your assumptions and be sure to not include them in the questions" |
   | **Question substitution** | Am I about to answer an easier question instead of the hard one? | Kahneman & Frederick, "Representativeness Revisited" (Cambridge *Heuristics and Biases*, Ch.2) |

   *Output artifact:* A written premise list, one line per row that has a premise to flag.

3. **Challenge each premise**（挑戰每個前提）
   *Action:* For each premise, answer in writing: Why must this be true? Under what conditions would it fail?
   *Output artifact:* A written challenge note per premise, even short (one line is fine).

4. **Reframe if needed**（必要時重新框定問題）
   *Action:* If any premise is shaky and the answer depends on it → either (a) verify the premise itself in Phase 2, or (b) rewrite the question to make the premise explicit.
   *Output artifact:* If reframed, **write out the new question verbatim** so the user can see the change in Phase 3 output.

**Hard requirement:** If any of the four written artifacts is missing, Phase 0.3 is incomplete and the run halts. Going to Phase 0.5 / Phase 2 without these artifacts means substitution was *declared* not *checked* — exactly the failure mode this Phase exists to prevent.

**Case study:** See `examples/case-political-substitution.md` — a 2026-05-11 case where a viral Threads post smuggled a false Authority premise ("the Hualien magistrate controls a 300-billion budget" — actually a central-government special budget) into a popular accusation. Phase 0.3's premise audit caught the substitution before Phase 2 began.

#### Output

Phase 0.3 produces an **internal artifact**: the original question + the reframed question (if reframed). This artifact is **shown to the user** in the final output (Phase 3) under "我把問題改成這樣問" / "Question reframing" — because exposing the reframing is itself part of the methodology's pedagogical value.

#### Why this Phase exists

Phase 0.5 (Pre-registration) prevents motivated search-term selection. But Phase 0.5 assumes the **question itself is well-formed**. If the question smuggles in a false premise, no amount of rigorous searching will produce a true answer — you'll get a precise answer to the wrong question.

This is Kahneman's *attribute substitution* applied to LLMs: when handed a hard question, the system tends to silently substitute an easier related one (e.g., "Is X policy good?" → "What do articles I can find say about X policy?"). Phase 0.3 forces conscious detection.

#### Lineage

This Phase synthesizes:
- **Structure**: CIA Tradecraft Primer's Key Assumptions Check (four-step procedure)
- **Falsifiability premise**: Popper
- **Stance-detection premise**: Maxwell qualitative methodology
- **Substitution-detection premise**: Kahneman & Frederick

Each row in the checklist traces to a specific T1/T2 academic source. This is documented in `examples/case-phase-0-3-genesis.md`.

### Phase 0.5: Pre-registration（預先登記）— from research ethics

**Before searching**, briefly state:
- What specific claim am I trying to verify
- What search terms I will use
- What T1/T2 source I expect to find this in

This prevents motivated reasoning (post-hoc cherry-picking of search terms that confirm a desired answer — the LLM equivalent of p-hacking).

事前聲明搜尋計畫，防止為了得到想要的答案，事後挑選搜尋詞。

### Phase 1: Scope Reconnaissance（範圍偵察）— optional

**Skip this phase if** the T1 source is obvious (e.g., TSMC investor relations for TSMC quarterly revenue).

**Run this phase if** the candidate set is unclear (e.g., "which Taiwanese players are in 2026 MLB").

```bash
# Detect Gemini availability
which gemini >/dev/null 2>&1
```

- **If Gemini available + scope unclear:** invoke Gemini as scout
  ```bash
  echo "List all candidate X for question Y. Output names only, with sources." | gemini
  ```
  Use Gemini's output as a **candidate list**, not as answers. Each candidate must be verified in Phase 2.

- **If Gemini unavailable:** use broad WebSearch with multiple query variations.

### Phase 2: T1/T2 Verification（驗證）— REQUIRED

For each factual claim, fetch the T1/T2 source directly. Do not synthesize from search snippets.

- Use WebFetch to retrieve the actual source page
- Cross-reference at least 2 **independent** sources for high-stakes claims
- Apply Citation Chain Integrity, Wire Service Detection, Time Context Decay rules

#### T1 Domain Gate — REQUIRED before labelling any source T1（強制 T1 域名規則）

Before any source can carry a T1 label in the final output, its domain must match the appropriate category below. If it does not match, the source is **T2 at best**, and the citation chain must be traced upstream to a real T1 document before Phase 2 is considered complete.

| Domain category | Acceptable T1 patterns | Examples |
|---|---|---|
| **Government / legislative** | `*.gov.tw`, `ppg.ly.gov.tw`, `lis.ly.gov.tw`, `law.moj.gov.tw`, official ministry/agency domains | Executive Yuan press, legislative bill PDFs, official statute database |
| **Statistical** | Original databases on government statistical agency domains | `dgbas.gov.tw`, `stat.gov.tw`, `nhi.gov.tw` raw data |
| **Academic / peer-reviewed** | Journal DOI pages, university research output (not campus newsroom) | `doi.org/*`, `*.edu` primary research pages |
| **Corporate** | The named company's own investor-relations / press domain — for that company's *own* claims only | TSMC investor relations for TSMC revenue |
| **International official** | Multilateral organisation primary documents | `un.org`, `who.int`, `worldbank.org` raw reports |

**News outlets are T2 at best, even when they appear authoritative.** This includes:
- Wire services: `cna.com.tw` (中央社), `reuters.com`, `ap.org`
- Public broadcasters: `pts.org.tw` (公視), `bbc.com`
- Newspapers / magazines: `udn.com`, `ltn.com.tw`, `chinatimes.com`, `storm.mg`, `newtalk.tw`, `nytimes.com`, `economist.com`

When a news article cites a primary document (e.g., a court ruling, a legislative bill, a corporate filing), Phase 2 must **follow the citation chain back to that primary document** before finalizing. If the upstream T1 cannot be located, the news source can be cited but **must be tier-labelled T2** in the verification output.

**Audit step before output:** Re-read every "T1" label in your draft. For each one, ask: "Does this URL's domain appear in the table above?" If no, downgrade or chase the upstream document.

**⚡ Parallelization rule:** When fetching multiple independent sources (different candidates, different domains, different aspects), issue all WebFetch / WebSearch calls **in parallel** in a single tool-use batch. Sequential execution wastes wall-clock time for no benefit. Only serialize when one fetch's result determines the next fetch's URL.

Example: Verifying 4 baseball players → 4 parallel WebFetch calls, not 4 sequential.

**The Iron Law applies here**: if the question requires data past your training cutoff, you MUST execute the search, not defer to the user.

**Case study:** See `examples/case-citation-laundering.md` — a 2026-05-11 failure where `udn.com` was mistakenly labelled T1, leading to five factual errors in a legislative bill comparison. The T1 Domain Gate was introduced specifically to prevent this recurrence.

### Phase 3: Disclosure Output（揭露式呈現）

#### Output Structure — Inverted Pyramid（倒金字塔）

Order content by **user importance**, not by phase order:

```
1. Conclusion / TL;DR             ← 2-3 sentence direct answer
2. (If question was reframed)     ← "我把問題改成這樣問" — show original vs reframed
3. Main content                    ← Structured details
4. Caveats / risks (if applicable) ← Real warnings only
5. Blind spots / additions         ← What might be missed
———— horizontal rule ————
6. Verification details            ← Sources / falsifiability / reproducibility
```

**Question reframing block (when applicable):**

If Phase 0.3 reframed the question, surface this near the top of the output. The reframing is itself part of the answer — it teaches the reader to detect smuggled premises. Format:

```
原本問題：[user's original phrasing or news headline]
重新框定為：[the reframed, premise-explicit version]
為什麼：[which premise was shaky and how the rewrite makes it explicit]
```

If the original question already had clean premises, omit this block (don't perform reframing as decoration).

#### Confidence Labels — Default to Verified, Label Exceptions Only

Estimative Language (from intelligence analysis):

| Label | When to use |
|-------|-------------|
| **(unlabeled)** | Default. Means: verified by T1/T2, no exception |
| **⚠ 【中信心】Likely** | Single T2 source, no cross-verification |
| **⚠ 【低信心】Possible** | T3-T4 source, or reasonable inference |
| **⚠ 【不採信】Speculation** | LLM-synthesized, no source support — explicitly excluded from conclusion |
| **⚠ 【已知衝突】Disputed** | Sources disagree — show both sides |

**Rule:** Do NOT label every section. Verified = silent default. Only label exceptions, so labels become signal, not decoration.

#### Phase Numbers Are Internal — Never Expose to User

The user does not care about Phase 4 / Phase 5. Translate internal phase outputs into user-facing language:

| Internal | User-facing label |
|----------|-------------------|
| Phase 4 (peer review) output | "潛在盲點" / "Other angles to consider" / "可補充的觀點" |
| Phase 5 (devil's advocacy) output | "反向觀點" / "反證檢驗" / "Counter-evidence check" |
| Phase 0.5 (pre-registration) | (Internal only — never shown in final output) |

Phase numbers in output = leaked implementation details = bad UX.

#### Verification Metadata — Grouped at End

Use a horizontal rule (`---`) to separate the answer from the verification trail. Group all metadata under a single heading:

```
---
## 查證細節

**來源 (Sources)**: [list with tier annotations]
**信心摘要**: [list only the non-default exceptions]
**反證條件 (Falsifiability)**: [what evidence would change this answer]
**搜尋紀錄 (Reproducibility)**: [terms, URLs, retrieval date]
```

This pushes metadata below the fold visually, while keeping it accessible for users who want to verify.

#### Falsifiability ↔ Phase 5 Relationship

The Falsifiability statement is the **passive** description ("If X were true, my answer would be wrong"). Phase 5 (when triggered) **actively searches** for whether X is in fact true. Phase 5 turns the falsifiability statement into investigation results.

#### Privacy / PII Discipline — REQUIRED before publishing or sharing output

Source-Trail produces analyses that are intentionally shareable: case files, examples directories, social-media posts, GitHub repositories. Before any verification output is committed to a publicly-discoverable surface, run this gate.

##### The two-tier rule

| Speaker category | Treatment |
|---|---|
| **Public figures speaking in public-conduct roles** — elected officials, government spokespeople, party caucuses, union representatives, named institutional actors, journalists writing under byline | **Name directly.** Their public statements about their public conduct are open to scrutiny by name. |
| **Private individuals speaking in personal capacity** — pseudonymous social-media handles, comment-section authors, individual users of any platform, named individuals who are not public figures | **Redact identity.** Quote the claim, describe the role (「一則留言」「該貼文作者」「a commenter」), but never surface the handle, real name, profile URL, or other identifier. |

##### When in doubt, redact

The cost of over-redaction is small (slightly less colorful case studies). The cost of over-naming is borne by someone other than the Skill author and is permanent once indexed. Default to redaction.

##### What counts as identifying information

- `@handles` on social platforms (Threads, X, Instagram, etc.)
- Real names of non-public individuals
- Direct URLs to individual posts or profiles
- Workplace + role + city combinations that practically identify someone
- Distinctive paraphrases that would be searchable back to the individual

##### When the claim is part of the analysis

If the goal is to analyze a *class* of incorrect claim (e.g., "social-media commenters often conflate central and local government budgets"), describe the class and quote one representative claim *without attribution*. Methodology lessons survive redaction; only the name is removed.

##### Public-platform availability is not a license

A Threads post or X reply being publicly viewable does not authorize lifting that speech into a permanent indexed methodology document attached to a verdict. Contextual integrity (Nissenbaum 2004) — the appropriateness of information flow relative to its original context — applies even to nominally public speech.

##### Audit step before commit / push

Before `git commit` or any publish action on output that will become discoverable:

1. Search the draft for `@`, real-name patterns (e.g. CJK personal-name conventions), and platform-specific identifiers
2. For each hit, ask: is this person a public figure speaking in a public-conduct role *as it relates to this claim*?
3. If yes — keep. If no — redact and replace with a role description.

**Case study:** See `examples/case-privacy-leak.md` — a 2026-05-11 failure where six private individuals' Threads handles were committed to the public Skill repository alongside verdicts of "factually wrong" on their comments. The Privacy / PII Discipline was added to Phase 3 in direct response.

### Phase 4: Peer Review（同儕審查）— conditional

**Run when:**
- Stakes are Medium or High
- Answer involves subjective judgment ("最強", "最佳")
- I am highly confident — high confidence warrants higher scrutiny

**Skip when:**
- Pure factual lookup at Low stakes
- Multiple independent T1 sources already converged

**Execution:**
- **If Gemini available:** submit answer to Gemini with prompt "你是同儕審查者，請找出此回答的盲點。"
- **If Gemini unavailable:** Claude self-roleplays as adversarial critic.

**Critical filtering rule:** Gemini's review will contain valid methodological critiques AND its own fabricated "specifics" (made-up version numbers, draft formats, etc.). Absorb the methodological critique, treat any new specific claims as unverified — must re-verify in Phase 2 if used.

### Phase 5: Devil's Advocacy（敵對審查）— conditional

**Relationship to Phase 3 Falsifiability:**
- Phase 3 Falsifiability = passive description ("If X were true, my answer would be wrong")
- Phase 5 Devil's Advocacy = active search ("Let me actually try to find evidence that X is true")

Phase 5 turns the falsifiability statement into an investigation.

**Run when:**
- Stakes are High (decision support, harm if wrong, contested claims)
- Comparative claims with multiple plausible answers ("最", "排名", "排行")
- The conclusion is counterintuitive or surprising
- The answer involves prediction or extrapolation
- Politically / socially contested topics

**Skip when:**
- Stakes are Low and Phase 2 produced 2+ independent T1 sources agreeing
- Answer space is closed and well-bounded (e.g., "list of currently active X")
- The Falsifiability statement in Phase 3 has no plausible trigger conditions worth searching

**Execution:**
1. **Find counter-evidence**: What evidence would disprove this conclusion? Search for it actively.
2. **List competing hypotheses (ACH)**: Beyond my answer, what other explanations fit the evidence?
3. **Indicators & Warnings**: Where am I structurally most likely to be wrong for this question type? (Sports? Stats lag. Tech? Versions shift. Politics? Wire bias.)

**⚡ Parallelization rule:** Counter-evidence searches across different angles are independent — issue all in parallel.

This is more proactive than Phase 4 — it doesn't wait for an external reviewer.

---

## Disclosure Format Examples

### ❌ Bad: Training-data Synthesis（訓練資料合成）

> Faker won the 2024 World Championship. He's averaging 4.3/1.5/3.4 KDA in the 2026 LCK Spring split, leading T1 to first place.

Problems: no sources, specific numbers without verification, mixes verified history with unverified current data, no confidence signal.

### ❌ Bad: Over-labeled Verification Dump（標籤氾濫）

> **【已驗證】**
> - Faker is on T1's 2026 roster
>
> **【已驗證】**
> - T1 won 2026 LCK Cup
>
> **【已驗證】**
> - T1 won Season Opening
>
> **Phase 4 — Self peer review:** Things I might have missed...
> **Phase 5 — Devil's advocacy:** Counter-evidence...
> **Reproducibility Log:** ...
> **Falsifiability:** ...
> **底線:** Faker remains top-tier.

Problems: every fact labeled (signal becomes noise), Phase numbers leaked to user, conclusion buried at the end after metadata.

### ✓ Good: Inverted Pyramid（倒金字塔結構）

> **Faker 仍是頂尖韓國中路選手，但「最強」沒有共識答案。**
> 韓國社群普遍把 Faker（國際賽/關鍵球）和 Chovy（個人對線/例行賽）並列在最頂層，Zeka、Showmaker 也仍在前段。
>
> ## 2026 季前賽戰績
> - T1 在 2026 LCK Cup 小組賽全勝開局
> - 拿下 2026 LCK Season Opening，Faker 在開幕對 HLE 一役獲選 MVP
> - 個人累積戰績：2025 世界賽奪冠後成為史上奪冠最多的選手
>
> ## 「最強」的不同定義
> - 國際賽 / 關鍵時刻 → Faker
> - 個人對線 / carry 能力 → Chovy（Gen.G）長期被視為更強
> - 純機械操作 → 年輕選手如 Zeus、Oner 在某些指標上更高
>
> ## 可補充的觀點
> 隊伍體系的加成沒有單獨拆出 — Faker 的穩定性受惠於 Keria 遊走和 Oner 控圖；如果單看「最強單兵」可能會給出不同答案。
>
> ⚠ **【中信心】**：場均數據（4.3/1.5/3.4 KDA）目前只查到 Sheep Esports 一個來源，未交叉驗證。
>
> ⚠ **【不採信】**：搜尋結果有摘要稱 "Faker 拿下 2026 MSI MVP"，但 MSI 2026 尚未開打，是搜尋引擎自動摘要的幻覺，已排除。
>
> ---
> ## 查證細節
>
> **來源**:
> - T1: T1 official site, Liquipedia
> - T2: Inquirer Esports, Sheep Esports, Taipei Times
>
> **反證條件**: 此答案會有誤，若 (a) Faker 自最新來源日後被冷凍，(b) Sheep Esports 統計有誤。
>
> **搜尋紀錄**: "Faker T1 LCK 2026 Spring performance"、"Faker 2026 LCK MVP best mid laner Korea" / 查證日期 2026-05-08

Note: verified facts presented as flowing prose without labels. Only the **non-verified exceptions** carry warning labels (⚠). No "Phase 4" / "Phase 5" leaked to user — translated to "可補充的觀點". Conclusion at top, metadata below the fold.

---

## Gemini's Dual Role

**Gemini is a tool, not a source.** It serves two distinct functions in this workflow:

### A. Scout（偵察兵）— Phase 1
- **Strength:** Google search integration → broader coverage of recent events
- **Use:** Identify candidates for "who/what" questions when scope is unclear
- **Output treatment:** Candidate list to verify, NOT final answer
- **Example:** "Which Taiwanese players are in 2026 MLB?" → Gemini lists names → Claude verifies each via T1

### B. Peer Reviewer（同儕審查者）— Phase 4
- **Strength:** Different model, different blind spots → catches what Claude missed
- **Use:** Critique a draft answer for methodological gaps
- **Output treatment:** Absorb methodological critique; re-verify any new "specifics" Gemini introduces
- **Example:** Gemini may correctly say "your KDA argument is shallow" while incorrectly fabricating "version 16.x" — keep the first, discard the second

### Common Mistakes（常見錯誤）

- ❌ "Gemini agrees with me, so the answer is right" — same training corpus = not independent
- ❌ "Gemini gave specific numbers, those are facts" — Gemini hallucinates specifics confidently
- ❌ Conflating roles — using peer review output as scout output, or vice versa

---

## Red Flags / Common Mistakes

| Mistake | What it looks like | Fix |
|---------|---------------------|-----|
| **Snippet synthesis** | Quoting numbers without fetching the source | Always WebFetch the actual source |
| **LLM cross-validation** | "Both Gemini and I say X" | Only T1/T2 verifies; LLMs don't |
| **Cutoff give-up** | "Beyond my training, please check Y" | Use the tools — that's why they exist |
| **Wire service double-counting** | "Reuters and Yahoo both confirmed" (both ran same AP wire) | Detect wire copy reuse |
| **Citation laundering** | "X reported Y said Z" without reading Y | Read original, not the retelling |
| **Specificity = accuracy fallacy** | Trusting numbers because they're precise | Precise hallucinations are still hallucinations |
| **Single-source confidence** | High confidence on one T2 source | Mark as "Likely" not "Verified" |

---

## Phase Selection Decision Tree

Use this to decide which phases to run. Phase 0, 0.3, 0.5, 2, 3 always run. Others depend on stakes and scope.

```
Question received
       ↓
[Phase 0] Is this factual?
   └─ No → Exit skill
   └─ Yes ↓
[Stakes Assessment] Low / Medium / High?
       ↓
[Pre-flight Announcement]
   └─ Low/Medium → announce + execute
   └─ High       → announce + WAIT for user confirmation
       ↓
[Phase 0.3] Key Assumptions Check (always)
   - List premises (existence, actor, time, metric, stance, substitution)
   - Challenge each
   - Reframe question if a premise is shaky
       ↓
[Phase 0.5] Pre-register strategy (always)
       ↓
[Phase 1] Is scope unclear?
   └─ Yes → Gemini scout (or broad WebSearch)
   └─ No  → Skip
       ↓
[Phase 2] T1/T2 fetch (always REQUIRED)
       ↓
[Phase 3] Disclosure output (always)
   - Estimative language labels
   - Reproducibility log
   - Falsifiability statement
       ↓
[Phase 4] Peer review?
   - Run if: Medium/High stakes, subjective judgment, high confidence
   - Skip if: Low stakes + 2+ independent T1 sources agreed
       ↓
[Phase 5] Devil's advocacy?
   - Run if: High stakes, comparative claims, surprising conclusion
   - Skip if: Low stakes + closed answer space + strong T1 verification
       ↓
Final answer
```

### Quick lookup: which phases by question type

| Question Type | Phases to Run |
|---------------|---------------|
| Pure factual lookup (Low stakes) | 0, 0.3, 0.5, 1?, 2, 3 |
| Comparative / evaluative (Medium) | 0, 0.3, 0.5, 1?, 2, 3, 4 |
| Decision support / harm if wrong (High) | All phases (0–5) |
| News / current events | 0, **0.3 critical**, 0.5, 1?, 2, 3, +5 if politicized |
| Technical specs / docs | 0, 0.3, 0.5, 2, 3 |
| Medical / financial / legal | All phases (0–5), no shortcuts |

**Note on news / current events:** Phase 0.3 is *especially* critical here — news headlines almost always arrive pre-framed by an editor's assumptions. Skipping Phase 0.3 means inheriting those assumptions silently.

---

## Advanced Techniques (Appendix)

Optional refinements for high-stakes verification:

### Right of Reply（給被指控方回應的機會）
For comparative claims ("X is better than Y"), actively seek Y's defenders' counterargument before publishing.

### Conflict of Interest Tagging
If a source is the party-of-record (company press release about itself, paid promotion), downgrade one tier and flag.

### Hierarchy of Evidence
Direct observation > Reported observation > Aggregated analysis > Individual opinion. T1 source quality varies by this hierarchy.

### Convergent Evidence
Beyond multi-source: multi-**method** convergence is stronger. Official announcement + third-party statistics + on-the-ground reporting all pointing to same conclusion = high confidence.

### Indicators & Warnings
Pre-list "where am I most likely to be structurally wrong" for this question type. Sports? Stats websites lag. Tech? Version numbers shift. Politics? Wire bias.

### Chain of Custody
Information from original to presentation must be traceable at every step. No "I read it somewhere" — track the path.

### Hearsay vs Direct Testimony
"X said that Y said Z" is weaker than "Y directly said Z." Prefer direct quotes over reported quotes.

### Exhibit > Testimony
Original documents (T1) outweigh expert opinion (T3). Link the document, don't paraphrase the expert.

---

## Real-World Cases

See `examples/` directory:
- `case-baseball.md` — Phase 1 Gemini scouting succeeds where solo WebSearch fails (2026 Taiwanese MLB players)
- `case-tsmc.md` — Phase 1 skipped when T1 source is obvious (TSMC quarterly revenue)
- `case-faker.md` — Phase 4 peer review's double-edged nature (Gemini's critique contains hallucinations to filter)
- `case-phase-0-3-genesis.md` — How Phase 0.3 was added to the skill: the meta-case of using Source-Trail to verify Source-Trail's own academic foundations (CIA KAC, Popper, Maxwell, Kahneman)
- `case-citation-laundering.md` — When news outlets are mistakenly labelled T1: a 2026-05-11 failure that introduced the T1 Domain Gate rule into Phase 2
- `case-political-substitution.md` — When a viral question smuggles a false Authority premise: a 2026-05-11 case that hardened Phase 0.3 from declaration-based to execution-based
- `case-privacy-leak.md` — When methodological rigor is not the same as ethical adequacy: a 2026-05-11 PII failure in the Skill's own published examples that introduced the Privacy / PII Discipline rule into Phase 3

---

## Origin

Distilled from a 2026-05-08 working session on epistemic humility in LLM-assisted research. Each rule traces to a documented failure mode caught during testing. See `examples/` for the original cases.

### Version history

- **v1.0** (2026-05-08) — Initial release. Phases 0, 0.5, 1, 2, 3, 4, 5. Origin from baseball/Faker/TSMC failure cases.
- **v1.1** (2026-05-11 AM) — Added Phase 0.3 (Key Assumptions Check). Triggered by recognizing that Phase 0.5 (pre-registration) assumes the question is already well-formed; if the question smuggles a false premise, no rigorous searching produces a true answer. Adapted from CIA Tradecraft Primer's KAC procedure. See `examples/case-phase-0-3-genesis.md`.
- **v1.2** (2026-05-11 PM) — Hardening release in response to two same-day failures during real-world testing:
  - **Added T1 Domain Gate** to Phase 2: a source cannot be labelled T1 unless its domain matches an explicit whitelist; news outlets are T2 at best regardless of authority. Triggered by mis-labelling `udn.com` as T1 in a legislative-bill comparison, which led to five factual errors. See `examples/case-citation-laundering.md`.
  - **Phase 0.3 substitution check is now execution-based, not declaration-based**: each of the four KAC steps must produce a written artifact; missing artifact halts the run. Added "Authority / Jurisdiction" as a distinct premise category. Triggered by a viral Threads post smuggling a false Authority premise that survived initial Phase 0.3 inspection. See `examples/case-political-substitution.md`.
- **v1.3** (2026-05-11 evening) — Ethics release. The v1.2 push committed six private individuals' Threads `@handles` into the public Skill repository alongside "factually wrong" verdicts on their specific comments. The Skill author caught this on review and required a privacy discipline that should have been built in from the start. Added **Privacy / PII Discipline** as a Phase 3 sub-rule, with a two-tier rule (public figures named directly; private individuals redacted). Retroactively redacted `examples/case-political-substitution.md`. Documented the failure as `examples/case-privacy-leak.md`. The git history of the v1.2 push still contains the original handles; whether to rewrite history via force-push is a deliberate user decision pending at the time of release.
