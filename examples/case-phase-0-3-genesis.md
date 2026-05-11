# Case: How Phase 0.3 was added — Source-Trail verifying itself

**Date:** 2026-05-11
**Question asked:** 「『Question Quality Check』（事實查核前的問題品質檢驗）在學術上有哪些權威傳統？我列的 5 個檢查項是否能各自溯源到 T1/T2 文獻？」
**Phase highlighted:** This is the meta-case. The skill was used to verify the academic foundations of a proposed addition to itself.

---

## Context: why this case matters

During a working session on launching a Threads account that publishes Source-Trail outputs, the user asked whether the skill should add a question-quality-check step *before* Phase 0.5 (Pre-registration). Claude initially proposed a "Phase 0.3" with five informal checks, attributing each to an academic tradition from memory.

The user then asked: **「你說的 Phase 0.3，有背後的嚴謹學術來源支撐嗎？」**

This forced an admission: the five checks were synthesized from training-data memory — T5 in Source-Trail's own classification system. None had been verified against T1/T2 sources. If Phase 0.3 were added without verification, it would be the only Phase in the skill that violated the skill's own standards.

The user then chose **Option A — run full Source-Trail to verify before adding**.

This case documents the verification.

---

## Phase 0 — Trigger

The question contains: "權威傳統" (attribution / authority claim), "有哪些" (existence claim about academic frameworks), "T1/T2 文獻" (specific verifiable references). Multiple triggers fire. Skill activates.

## Phase 0.3 — Key Assumptions Check (added retroactively)

This case predates Phase 0.3's official addition, but in retrospect, the question's premises were:
- (existence) Each of the 5 checks has *some* academic tradition behind it
- (substitution) Claude is not silently answering "do these sound plausible?" instead of "are these actually supported?"
- (stance) The user wants honest evaluation, not validation of Claude's prior claims

All three premises held; no reframing needed.

## Phase 0.5 — Pre-registration

Stated before searching:
- **Specific claim to verify:** Each of 5 traditions (Popper falsifiability, Bridgman operationalization, Five Ws, Maxwell qualitative methodology, Kahneman question substitution) has a documented academic source supporting the connection Claude claimed
- **Search terms planned:** Author + concept + work title for each
- **Expected T1/T2:** Original works (Popper 1934, Bridgman 1927, Maxwell 3ed, Kahneman 2011) + Stanford Encyclopedia + Cambridge Heuristics and Biases
- **Failure modes anticipated:** (a) over-rationalization — finding citations that look right but talk about something different; (b) discovering an existing integrated framework that supersedes the ad-hoc 5-item list

## Phase 1 — Scope Reconnaissance

Skipped. Each tradition has obvious T1 sources; scope was clear.

## Phase 2 — T1/T2 Verification (8 parallel WebSearch + 3 WebFetch)

Results, by claim:

| Original claim | Verification outcome | Source tier |
|---|---|---|
| Popper supports "question must be factually answerable" | **Verified.** Popper explicitly links falsifiability with testability as the criterion for scientific status. | T1 (Britannica direct quote of Popper), T2 (Stanford Encyclopedia) |
| Maxwell supports "question shouldn't presuppose its answer" | **Verified.** Maxwell *Qualitative Research Design* (3ed) explicitly: "Pay attention to your assumptions and be sure to not include them in the questions" | T2 (Sage Handbook) |
| Kahneman substitution explains why Phase 0.3 exists at all | **Verified, and stronger than initially claimed.** Kahneman & Frederick's three conditions explicitly include "the substitution is not detected and corrected by the reflective system" — Phase 0.3 *is* the reflective-system check. | T1 (Cambridge *Heuristics and Biases* Ch.2), T2 (Wikipedia direct quote) |
| Bridgman operationalization supports "scope/unit clarity" | **Falsified.** Stanford Encyclopedia: Bridgman did not require pre-research operational definitions; second-generation operationists treat them as "provisional tools aiding investigation." The connection Claude proposed was an over-stretch. | T1 contradicts the claim |
| Five Ws supports "time anchor / actor unit" | **Partially correct but role miscast.** Five Ws is a *content checklist for written news leads* (1913 onwards), not a *pre-investigation question-quality framework*. The structural overlap is real but the methodological role is different. | T3 (Wikipedia) |

### Phase 5 — Devil's Advocacy (counter-evidence search)

Active search for: "Does an existing integrated framework already exist that does what Phase 0.3 proposes?"

**Result: YES — and finding it is the most important outcome of this case.**

The CIA Tradecraft Primer (2009) describes the **Key Assumptions Check (KAC)** as a structured analytic technique used **at the beginning of an analytic project**. Its four-step procedure is:

1. Review what the current analytic line appears to be
2. Articulate all the premises, both stated and unstated
3. Challenge each assumption: why "must" it be true?
4. Refine the list of key assumptions

Direct quote (Tradecraft Primer p.7):
> "A Key Assumptions Check is most useful at the beginning of an analytic project. ... Identifying hidden assumptions can be one of the most difficult challenges an analyst faces, as they are ideas held — often unconsciously — to be true and, therefore, are seldom examined and almost never challenged."

**This is exactly the function Phase 0.3 was meant to serve.** The DC Sniper case study in the Primer (pp.7-8) shows how skipping KAC led investigators to assume the sniper was "white, male, military-trained, driving a white van" — premises that turned out partially false and narrowed the suspect pool wrongly.

### Phase 4 — Peer review (informal)

Methodological critique applied: instead of inventing a new term ("Question Quality Check"), adopt the established CIA term ("Key Assumptions Check") and cite the Primer directly. This:
- Gives Phase 0.3 a real T1 lineage rather than synthetic origins
- Connects Source-Trail to the established intelligence-analysis tradition it already draws from
- Avoids the credibility tax of introducing yet another term

## Phase 3 — Disclosure Output

Final design decision:

**Phase 0.3 = Key Assumptions Check (前提核查)**, structured as CIA's four-step KAC, with each premise type (existence / actor / time / metric / stance / substitution) anchored to its corresponding tradition (Popper / general analytic clarity / Maxwell / Kahneman).

The Bridgman/operationalization claim was dropped. The Five Ws role was downgraded to "cultural reference, not methodological foundation." Two new T1 anchors (CIA KAC; Kahneman & Frederick) replaced them.

---

## What this case teaches

1. **The skill must apply to itself.** A methodology that demands T1/T2 verification for factual claims must hold its own additions to the same standard. If Phase 0.3 had been added based on memory-synthesized correspondences, every future use of the Phase would inherit that initial epistemic debt.

2. **Phase 5 (Devil's Advocacy) saved the design.** The original 5-item informal list was structurally fine but lacked a unifying lineage. Asking "does an existing framework already do this?" surfaced CIA KAC, which is both more authoritative and more concrete than what Claude had drafted. The lesson: when proposing new frameworks, actively search for prior art *before* finalizing.

3. **Falsified premises are not failures — they're the point.** The Bridgman correspondence was wrong. Discovering that strengthened the final design (no false foundation) and demonstrated the skill working as intended. A pre-registration that prevents you from quietly dropping disconfirmed claims is doing its job.

4. **"From memory" is T5.** Even when Claude's memory of an academic tradition is *correct in spirit*, it is not evidence — it is a hypothesis to test. The Maxwell correspondence happened to verify; the Bridgman one happened to fail. Without Phase 2, Claude could not have known which.

---

## Sources verified in this case

**T1:**
- CIA *A Tradecraft Primer: Structured Analytic Techniques for Improving Intelligence Analysis* (2009), pp.7-8 — Key Assumptions Check section, direct PDF extraction
- Karl Popper, *The Logic of Scientific Discovery* (1934/1959) — falsifiability/testability link via Britannica direct quote
- Daniel Kahneman & Shane Frederick, "Representativeness Revisited: Attribute Substitution in Intuitive Judgment" — Cambridge *Heuristics and Biases* (2002), Ch.2

**T2:**
- Stanford Encyclopedia of Philosophy: "Operationalism" — used to falsify the Bridgman correspondence
- Joseph Maxwell, *Qualitative Research Design: An Interactive Approach* (3ed) — via Sage Handbook
- Wikipedia: "Attribute substitution" — used for direct-quote verification of Kahneman & Frederick's three conditions

**T3:**
- Wikipedia: "Five Ws" — sufficient for downgrading the Five Ws role to "cultural reference"

**Search executed:** 2026-05-11. 8 parallel WebSearch queries + 3 WebFetch + 1 PDF text extraction (CIA Tradecraft Primer).

**Falsifiability:** This case's conclusion would be wrong if (a) the 2009 Tradecraft Primer's KAC section was substantially revised in later editions in ways that change its meaning — the 2009 PDF was used and not cross-checked against newer editions; (b) Maxwell's "avoid assumptions in questions" passage was misquoted in the Sage Handbook summary I relied on rather than the original book.
