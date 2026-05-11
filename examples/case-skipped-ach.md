# Case: Phase 5 Skipped — When ACH Is Written but Not Executed

**Date:** 2026-05-11
**Phase highlighted:** Phase 5 (Devil's Advocacy) — moving ACH from a *listed step* to a *required written artifact*.

---

## What went wrong

While running Source-Trail on the Hualien "300 億" question (see `case-political-substitution.md`), I produced a Phase 5 output that *named* the right techniques but did not *execute* the central one.

What I wrote at Phase 5:

> "Counter-evidence searches across different angles are independent — issue all in parallel."
> "I searched for 'why the KMT version is good for fact-checking' but found no systematic articulation."

What I did NOT write:

> "Competing hypotheses for what '300 億' refers to: H1 Matai'an dam special budget, H2 0403 earthquake reconstruction plan (285.5 億), H3 sum of both, H4 unknown third source. Evidence for each. Confidence per hypothesis."

The skill's own Phase 5 instructions say:

> **List competing hypotheses (ACH): Beyond my answer, what other explanations fit the evidence?**

This was in the instructions. I read it. I did not do it.

The user caught the gap on review by asking: *"285 億 跟 300 億 很接近，會不會其實大家在罵的是這個？"* — exactly the H2 alternative that Phase 5 should have surfaced before the report was written.

---

## Why this matters

Phase 5's purpose is to find the counter-explanation *before* it becomes embarrassing for the analyst. Skipping ACH means the report ships with **only the analyst's preferred hypothesis defended**, even when alternatives are plausible.

In this specific case, the cost was modest:

- Re-running ACH confirmed H1 (Matai'an dam) as ~75-80% likely
- H2 (0403 plan) was ~15-20% — non-trivial but not dominant
- The *core conclusion* ("county magistrate has no disbursement authority") held under both H1 and H2

So the final answer survives. But the report's *credibility* would have been higher if it had presented H1 + H2 from the start, rather than appearing to have only considered one option.

In a harder case, the failure mode would be worse: an unsurfaced H2 might actually be the right answer, and the analyst would publish a confident wrong conclusion.

---

## Root cause

Phase 5's checklist mentioned ACH as one of three sub-steps:

```
1. Find counter-evidence
2. List competing hypotheses (ACH)
3. Indicators & warnings
```

But it described ACH as a *capability* rather than a *required artifact*. The wording "Beyond my answer, what other explanations fit the evidence?" reads like a prompt for self-reflection — and self-reflection without enforcement gets skipped under time pressure or when the analyst is confident in the preferred hypothesis.

This is the same failure pattern that produced the v1.2 → v1.3 change to Phase 0.3 (declaration-based check → execution-based check with required written artifacts). Phase 5 needs the same hardening.

---

## The fix

### Structural change to SKILL.md Phase 5

ACH becomes a **required written artifact** when Phase 5 runs, with a minimum format:

```
Competing hypotheses:
| Hypothesis | Description | Evidence for | Evidence against | Confidence |
|---|---|---|---|---|
| H1 | [primary answer] | [...] | [...] | [%] |
| H2 | [alternative] | [...] | [...] | [%] |
| H3+ | [further alternatives if plausible] | [...] | [...] | [%] |
```

The table must contain **at least 2 hypotheses** (H1 and at least one non-trivial alternative).

If the analyst cannot generate a plausible H2, Phase 5 must explicitly state: *"No alternative hypothesis with non-trivial fit to the evidence was found"* — making the absence a deliberate finding, not an oversight.

### Re-frame of Phase 5's output structure in Disclosure (Phase 3)

When Phase 5 ran, the resulting report must surface the ACH table near the conclusion. If only one hypothesis is shown, the report invites the question "what else might this be?" — which is the very question Phase 5 exists to answer in advance.

In the Hualien 300 億 case, the report was updated post-hoc to include the ACH table. Future runs should produce it natively.

### Connection to Phase 0.3 hardening (v1.2)

Phase 0.3 was hardened in v1.2 to require written artifacts because "substitution detection by declaration" was insufficient. Phase 5 ACH has the same failure mode and needs the same hardening. This is a generalizable pattern: any methodology step that depends on "thinking carefully about X" without producing artifact-X tends to silently degrade into "I considered X."

The fix in both cases is the same — make the artifact mandatory, not the consideration.

---

## What this case teaches

1. **Documented does not mean executed.** Writing "List competing hypotheses" in the Skill description and listing competing hypotheses in an actual case run are not the same act. Without enforcement, the description is decoration.

2. **The analyst's preferred hypothesis is the gravity well.** Once a plausible H1 is found and supported, the energy to surface H2 drops sharply. Mandatory written ACH counteracts this gravity.

3. **The user is part of Phase 5.** Lion's question about 285 億 was an external execution of Phase 5 that the internal run skipped. This is fine — Source-Trail is collaborative, not autocratic — but the internal run should not depend on the user playing devil's advocate every time.

4. **Failure modes generalize.** The fix that hardened Phase 0.3 (declaration → execution + written artifact) applies cleanly to Phase 5. When a fix generalizes across phases, it is a sign that the methodology is converging on a deeper principle: *every claim about what was checked must produce an artifact of the check.*

---

## Lineage

- Trigger: Lion's review on 2026-05-11, after the Hualien 300 億 case study had already been redacted for PII and published.
- Failure mode: Phase 5 ran but skipped its ACH sub-step.
- Fix: SKILL.md Phase 5 — ACH made a required written artifact; halt the run if missing. See SKILL.md v1.4.
- Retroactive remediation: `cases/001-hualien-300-billion.md` was updated to include the ACH table between H1 and H2.
