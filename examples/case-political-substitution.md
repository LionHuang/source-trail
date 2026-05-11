# Case: Political Substitution — When the Question Itself Smuggles a False Premise

**Date:** 2026-05-11
**Question asked:** 「根據這篇 Threads 文章的討論，花蓮縣政府拿到的三百億用到了哪裡？」
**Phase highlighted:** Phase 0.3 (Key Assumptions Check) catching attribute substitution before search begins.

---

## Context

A user asked me to verify a viral Threads post (`[handle-redacted]`, 2026-05-11) accusing 「國王夫妻」(Fu Kun-chi / Hsu Chen-wei, husband-and-wife political dynasty governing Hualien) of misappropriating "300 億" of public funds. The post was framed as a critique of a volunteer mobilization effort (`[handle-redacted]`) for the Matai'an Creek flood cleanup.

By the time I read the post, the framing had been repeated dozens of times in the comment section as accepted fact:
- "拿了 300 億，結果沒有 1 元幫助到花蓮人" (`[handle-redacted]`)
- "沒人敢去質疑那 300 億的去向" (`[handle-redacted]`)
- "三百億不去追討" (`[handle-redacted]`)

The user explicitly chose 「做法一」(verify the post's claims against T1 sources), which made the substitution risk explicit before search began.

---

## What Phase 0.3 caught

Before any search, the Key Assumptions Check produced this premise audit:

| Premise type | What the post assumes | Challenge |
|---|---|---|
| **Existence** | 「三百億」 exists as a single discrete sum | Which 300 億? 0403 地震款? 馬太鞍堰塞湖款? 縣府年度預算? Multiple distinct envelopes plausibly fit. |
| **Actor / Authority** | 「國王夫妻把 300 億花到哪裡去了」 — implies Fu/Hsu can disburse the money | If it's a 中央特別預算, the Hualien County Government has *no* legal authority to disburse it. Premise depends on jurisdiction. |
| **Time anchor** | 「拿到」implies past-tense receipt | Special budgets often have multi-year execution windows. "Received" vs "executed" vs "audited" are three different states. |
| **Metric** | 「用到哪裡」assumes line-item traceability is the right question | Budget appropriation vs budget execution vs final audit produce three different answers; the user-facing public dataset only exposes the first. |
| **Embedded stance** ⚠️ | The verb 「把 X 花掉」presupposes wrongdoing | Phrasing "where did they spend it" pre-frames the answer as misappropriation. |
| **Substitution** ⚠️ | The "easy" version of this question is: summarize what Threads commenters say | The "hard" version: trace the 300 億 to its legal authorization document, identify the executing agency, and audit. These produce opposite answers. |

The two starred rows triggered an explicit halt: **the question as posed cannot be answered without first verifying whether the 300 億 figure is even tied to the county government in the first place.**

---

## What Phase 2 verification revealed

Running the *reframed* question — "Where does the 300 億 figure originate, and which government level holds disbursement authority?" — produced:

### The 300 億 is the 馬太鞍溪堰塞湖災後重建特別預算

- **Source act:** 《馬太鞍溪堰塞湖災後重建特別條例》, passed third reading on 2025-10-31
- **Cap:** 300 億元 (total ceiling under the special act)
- **Appropriation:** 270 億 編列 + 30 億 預留, approved by Executive Yuan 2025-11-13, passed legislature 2025-12-02
- **Execution period:** 2025–2030
- **Funding mechanism:** 100% debt issuance

### Where it sits in the budget hierarchy

This is a **central-government special budget**, executed by:
- Public Construction Commission (`reom.pcc.gov.tw`) — coordination
- Ministry of Agriculture — water systems, sediment control, agricultural facilities
- Ministry of Economic Affairs — water infrastructure
- Ministry of Transportation — Matai'an Bridge, road repair
- Ministry of the Interior — home reconstruction, disaster relief
- Hualien County Government — local implementation support (not budget controller)

**The county government does NOT control this 300 億.** Fu Kun-chi and Hsu Chen-wei, as past and current county magistrates respectively, have no legal authority to "spend" it. The post's central premise is structurally false.

### What the 270 億 is actually allocated to (中央特別預算書, T1)

- 經濟發展支出 192 億
- 社區發展及環境保護支出 56 億
- 預備金 15 億
- 一般政務支出 6 億
- 社會福利支出 1 億

---

## What the post's commenters got wrong vs. right

| Claim | T1 Verdict |
|---|---|
| 「國王夫妻把 300 億花到哪裡去了？」 | ⚠ Premise error: the money is not in their hands |
| 「沒有 1 元幫助到花蓮人」 (`[handle-redacted]`) | ⚠ Factually wrong: budget was passed 2025-12-02 and is in execution; recipients confirmed |
| 「沒人敢去質疑那 300 億的去向」 (`[handle-redacted]`) | ⚠ Factually wrong: the budget book is public, line items are itemized, legislators reviewed it |
| 「還有 0403 的賑災款，不知道那些房子修好沒」 (`[handle-redacted]`) | ✓ Reasonable question; this is a separate envelope (285.5 億, 2024-05-02) with partial T1 progress reports |

---

## What this case teaches

### 1. The question itself is sometimes the failure point

Phase 0–2 of Source-Trail are designed to verify *claims*. But a viral question can smuggle in a false premise so deeply that "verifying the claim" still produces a wrong frame. Phase 0.3's role is to surface and challenge premises *before* the question is operationalized.

If Phase 0.3 hadn't fired here, the natural search path would have been: "花蓮 300 億 用途" → news articles about the county government → false confirmation of the post's framing. The verification process would have ratified the framing it was supposed to test.

### 2. "Where did the money go" is the wrong question when "who controls the money" hasn't been established

This is a classic Kahneman attribute substitution: the original-and-hard question ("does the political accusation rest on factual foundations?") gets replaced by the easier-and-tractable one ("can I find line items for 300 億?"). Both might produce specific numbers. Only one is actually responsive to the user's real need.

### 3. Verification can clear the accused without endorsing them

The verified finding here — "the county government can't have misappropriated this money because it isn't theirs to spend" — is not a defense of Fu Kun-chi or Hsu Chen-wei. It is a factual correction of *one specific accusation*. Other accusations (about other budgets, other behaviours, other periods) remain open. Source-Trail's job is to clear the factual record, not to advance or rebut a political stance.

### 4. Threads as a question-generator is high-yield, high-risk

Threads posts are useful inputs for Source-Trail because they expose what *frames* are circulating in real time. They are dangerous inputs because the frames have already been internalised by hundreds of commenters who treat them as background fact. Every time Source-Trail processes a Threads-originated question, Phase 0.3 has to do extra work to peel the framing off the question.

---

## What changed in SKILL.md (v1.2, 2026-05-11) as a result

- **Phase 0.3 substitution check now runs as an action, not a declaration** — the run must produce a written list of premises and a written reframing before search begins. If the reframing doesn't happen, the run halts.
- **Phase 0.3 explicitly flags "Authority / Jurisdiction" as a premise category** — separate from Actor — because political accusations frequently smuggle false authority claims (X controls Y, when in fact Z controls Y).
- **Phase 3 output template now reserves a "我把問題改成這樣問" block as a top-of-output element** — making the reframing visible to readers, not buried in metadata. The visible reframing is itself pedagogical: it teaches readers what kind of question would have been answerable.

---

## Sources verified at T1

- `ey.gov.tw/Page/9277F759E41CCD91/cf3959dc-1a7b-41a8-9b2c-5a474960bfb9` (Executive Yuan special budget passage notice, 2025-11-13)
- `reom.pcc.gov.tw/` (Public Construction Commission post-disaster reconstruction portal)
- `hl.gov.tw/0403/` (Hualien County Government 0403 earthquake info portal)
- `hl.gov.tw/ysh/` (Hualien County Matai'an dam disaster portal)
- `moi.gov.tw/News_Content.aspx?n=4&s=326831` (MOI 0403 anniversary statement)

## Search trail

- WebSearch queries: 8 parallel + 1 reframing
- WebFetch: 3 (Executive Yuan, MOI, Hualien County)
- Verification date: 2026-05-11

## Falsifiability

This case's conclusion would itself be wrong if:
- The special-budget law has a clause delegating disbursement to the local government (no such clause exists in the version of the act searched on 2026-05-11);
- The Audit Office report (not yet published as of search date) reveals that the central executing agencies have in fact channeled the budget through county-government accounts in a way that grants Fu/Hsu effective control;
- A future news report uncovers a side-agreement between the central government and the county that I cannot see from the public T1 sources.

Each of those would necessitate a re-run of Phase 2 with revised premise.
