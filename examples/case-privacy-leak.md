# Case: Privacy Leak — Methodological Rigor Without Personal Discipline

**Date:** 2026-05-11
**Phase highlighted:** Phase 3 (Disclosure Output) — adding a privacy / PII gate before publication.

---

## What went wrong

While documenting `case-political-substitution.md` for the Skill's public `examples/` directory, I directly named six individual Threads users by their `@handles`:

- The author of the post being analyzed
- The author of a separate volunteer-mobilization post being criticized
- Four individual commenters whose specific claims were quoted and judged "factually wrong"

The v1.2 commit, pushed to `github.com/LionHuang/source-trail`, briefly made these handles part of a permanent, searchable, public methodology document — until the leak was caught and remediated.

The user — Lion, the Skill author — caught this on review, with the diagnosis:

> 「應該不能把 example 放上來，例如剛剛花蓮這則，等於是把別人的個人資訊也放上來了。因為這裡是直接指涉到真人（而非不特別人士、公眾人士）這裡要做脫敏混淆。」

He was right. The Skill's rigor on factual sourcing did not extend to the dignity of the *individuals whose claims were being checked*. Source-Trail can verify a claim while still protecting the speaker.

---

## Why naming was a mistake

The handles were all from publicly accessible Threads posts. The legal availability of the information is not the issue. The issues are:

### 1. Contextual integrity violation

A casual comment under a Threads post lives inside a specific social context. Lifting that comment into a public methodology document, attached to the analytical verdict "factually wrong", strips the original context (a quick reply, possibly to a small audience) and replaces it with a new context (a permanent case study of bad reasoning).

The original speaker did not consent to that recontextualization. Even if the comment is "still publicly visible on Threads", quoting it by handle in a different venue exceeds the original social bargain.

### 2. Public figures vs. private individuals

There is a meaningful distinction between:

- **Public figures acting in public roles** (elected officials, government spokespeople, union representatives, party caucuses) — their claims about their official conduct are open to detailed scrutiny, including by name. Source-Trail naming them when their public statements are factually wrong is appropriate.
- **Private individuals expressing views in their personal capacity** (anonymous or pseudonymous social-media users) — their incorrect beliefs may be analyzed as a *class of error*, but they themselves are not the subject of public accountability.

The case study confused these. It named private individuals as if they were public figures whose statements warrant institutional pushback.

### 3. Asymmetric stakes

The Skill is published on a discoverable platform (GitHub). The individuals quoted are not. A web search of any of those handles, now or in the future, could surface a Skill case study labelling their specific words "factually wrong." That is a disproportionate footprint for what was, in many cases, a single emotional reaction in a comment section.

### 4. The Skill is supposed to lower asymmetry, not amplify it

Source-Trail exists in part to counter the social-media dynamic where loud, framed claims overpower careful analysis. If the Skill's outputs themselves single out individuals for permanent indexed criticism, it begins to participate in the same asymmetric dynamic it is meant to neutralize.

---

## The fix

### Immediate

- All six `@handles` in `case-political-substitution.md` were redacted and replaced with role descriptions ("the post's author", "one commenter", "a commenter raising 0403 funds").
- The corresponding analytical content was preserved verbatim — the methodology lesson is unchanged. Only the names were removed.
- A note was added at the top of the redacted block explaining the redaction and pointing to this case.
- The same redaction was applied to the public-facing reader report (`source-trail-cases/001-hualien-300-billion.md` in Lion's threads-account repo).

### Structural — added to SKILL.md as a Phase 3 sub-rule

A new sub-rule under Phase 3 Disclosure Output: **Privacy / PII Discipline**. See SKILL.md for the full rule. Summary:

- **Public figures (in their public-conduct claims):** name them directly.
- **Private individuals (in personal-capacity social-media speech):** describe the *type* of claim and the *role* of the speaker; do not surface handles, real names, profile URLs, or any other identifying information.
- **When in doubt:** treat as private. The cost of over-redaction is small; the cost of over-naming is borne by someone other than the Skill author.

### Git history

After the v1.2 push that introduced the leak, two repair options were available:

1. **Forward-only repair:** redact on `main`, leave history alone. PII would remain visible to anyone running `git log -p` or browsing file history on GitHub, but the current `main` state and future indexes would be clean.
2. **History rewrite (force-push):** use `git filter-repo` to strip the handles from every prior commit, then `git push --force`. Destructive to anyone who had cloned or forked, but cleaner removal.

The Skill author chose **option 2 (history rewrite)** on the grounds that "this concerns other people's personal information, and the cost of treating it lightly is not ours to absorb." The handles were removed from every commit in the repository's history before search-engine indexers had time to fully fetch the v1.2 state. The resulting commit hashes for the v1.2 release and earlier no longer match what was originally pushed.

This is the right ordering of priorities for a Skill whose entire premise is accountable claim-making.

---

## What this case teaches

1. **Methodological rigor ≠ ethical adequacy.** A Skill that polices factual sourcing can still violate informational dignity. The two disciplines have to be specified separately and both enforced.

2. **"Publicly visible" is not a green light.** The legal default for public-platform speech is not the only constraint. Contextual integrity is.

3. **Anonymity is a privacy mechanism in its own right.** When a Threads commenter uses a `@handle` of the form `@<alphanumeric>` (e.g., a username followed by digits), the handle *is* their pseudonymity. Lifting that handle into a permanent indexed document is a meaningful identity escalation, even though the handle is "already public."

4. **The Skill must apply to its own examples.** Just as `case-citation-laundering.md` showed Source-Trail violating its own T1 rule, this case shows Source-Trail violating a discipline it had not yet articulated. The remedy in both cases is the same: name the failure, write the rule, apply the rule everywhere retroactively.

5. **The author of the Skill is closer to its risks than the user.** Lion is the one whose GitHub account hosts the Skill. The cost of an unredacted case lands on his name and his readers, not on Claude. The Skill's design should make it easy for the author to enforce his own privacy standards — including by halting publication when those standards are unclear.

---

## Lineage

- Trigger: Lion's review on 2026-05-11, after the v1.2 push.
- Caught at: visual inspection of the public Skill repo via browser, not by any automated rule.
- Result: SKILL.md v1.3 — added Privacy / PII Discipline as a Phase 3 sub-rule. Both the `case-political-substitution.md` and the reader-facing case study were redacted before any further public distribution.
