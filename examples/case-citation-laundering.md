# Case: Citation Laundering — When News Reports Get Mistaken for T1

**Date:** 2026-05-11
**Question asked:** 「三班護病比的法案，國民黨、民眾黨、民眾黨，三個黨個別提出的版本是什麼，差異在哪裡，後續效應為何？」
**Phase highlighted:** Phase 2 — the tier classification rule was silently violated when news outlets were labelled as T1.

---

## What went wrong

After running 8 parallel WebSearch queries + 3 WebFetch calls on UDN, Storm Media, Newtalk, etc., I produced a confident-looking comparison table of the three parties' versions of the Medical Care Act amendment:

| 我之前的整理 | 來源（**我標為 T1**）|
|---|---|
| 國民黨版：衛福部直接訂定、不設委員會、每 3 年檢討 | udn.com news article |
| 民眾黨版：委員會 9-11 人，護理代表 ≥ 1/2 | udn.com news article |
| 民進黨版：委員會三方各 1/3 | udn.com news article |

**Then the user asked:** 「你說的 T1 來源是《醫療法》第 12 條三讀條文，但這個連結是 UDN 新聞的轉述，並不是 T1。」

He was right. By Source-Trail's own tier definitions:

| Tier | Type | Example |
|------|------|---------|
| T1 | Primary / Original | 立法院議事文書、議案 PDF、全國法規資料庫 |
| T2 | Authoritative Secondary | 中央社 / 公視（**這才是新聞媒體的合理位階**）|
| T3 | Reported with citation | 一般網路新聞引用通訊社 |

**`udn.com` is a T2 outlet at best**, and the specific article I cited was paraphrasing legislative content. Labelling it T1 violated Source-Trail's *Citation Laundering* red flag, which the SKILL.md itself explicitly warns against.

This is the worst kind of failure: I had memorized the rule, I had written the rule into the Skill, and I still broke it.

---

## What re-running with real T1 revealed

After the user pushed back, I re-ran Phase 2 targeting `ly.gov.tw / ppg.ly.gov.tw / law.moj.gov.tw` only. I found:

1. **Direct PDF: 立法院第 11 屆第 5 會期第 9 次院會議案關係文書 (羅廷瑋等 17 人版)**
   `https://ppg.ly.gov.tw/ppg/SittingRelatedDocumentReportMatter/download/agenda1/02/pdf/11/05/09/LCEWA01_110509_00254.pdf`

2. **Direct DOC: 民眾黨黨團版 (議案 11011663)**
   `https://ppg.ly.gov.tw/ppg/bills/202110116630000/details`

3. **No legislative document found for the DPP "1/3" version** — most likely a floor-amendment motion (修正動議) on 5/8, not a formally registered bill.

Comparing the **actual statutory text** to my earlier news-derived table revealed multiple factual errors in what I had told the user:

| What I said based on news | Actual T1 text |
|---|---|
| "三黨差異只在一個條文上" | TPP version amends 3 articles (12, 102, 102-1), KMT amends 2 (12, 102) |
| TPP committee reviewed "每 3 年" | TPP version says **"每年定期檢討、調整或增減"** — annual review, not triennial |
| Committee members were "護理代表" | Statute uses **"護產相關專業團體"** — explicitly includes obstetric professionals |
| TPP penalty range 5–50 萬 | TPP version: 5–50 萬 (correct) |
| KMT penalty: tiered 25–200 萬 | KMT proposal: **flat 10–50 萬** — the tiered amounts came from a *later floor amendment*, not the original bill |

Five factual errors, all traceable to one root cause: trusting news outlets to faithfully transcribe legislative text.

---

## Root cause analysis

### Why this happened

1. **News convenience trap:** UDN articles bundled "comparison tables" of the three versions in one place. T1 documents are scattered across multiple PDFs on `ppg.ly.gov.tw`. The energy gradient pulled toward the convenient answer.

2. **Wire copy amplification:** Multiple outlets ran similar tables. The convergence felt like cross-validation, but they all traced to the same originating reporters who themselves had condensed the bills.

3. **My own Phase 0.3 didn't catch it:** The Phase 0.3 in the new SKILL.md mentions substitution detection. But I wrote it as a *declaration* ("I will check") rather than an *execution* ("I will fetch ly.gov.tw and verify the article number matches the actual bill"). Declaration without execution is theatre.

4. **Domain-level T1 gating was absent from Phase 2:** Nothing in SKILL.md required Phase 2 sources to come from a registered T1 domain. The Tier table existed but had no enforcement hook.

### Why this is the most dangerous failure mode

A news-derived answer that looks like a verified answer is worse than no answer at all:
- It transfers the news outlet's framing into "Source-Trail certified" territory
- The user trusts the badge, not the search
- The same outlets that politically frame stories now get their framing laundered through a tool that claims to filter framing

This is the kind of failure that, repeated at scale, would turn Source-Trail into the very thing it claims to prevent.

---

## Fixes implemented in SKILL.md (v1.2, 2026-05-11)

### Fix 1: Phase 2 T1 Domain Whitelist (new sub-rule)

Before any source is labelled T1 in the verification output, it must pass a domain check:

- **Government / legislative T1**: `*.gov.tw`, `*.ly.gov.tw`, `ppg.ly.gov.tw`, `law.moj.gov.tw`, official ministry domains
- **Academic T1**: `*.edu` or `*.edu.tw` primary sources (NOT campus news)
- **Statistical T1**: original databases (`dgbas.gov.tw`, `stat.gov.tw`, etc.)
- **Corporate T1**: company's own investor-relations or press domain, for that company's own claims only

If a candidate source is a news outlet (`udn.com`, `ltn.com.tw`, `chinatimes.com`, `cna.com.tw`, `pts.org.tw`, `newtalk.tw`, etc.), it is **T2 at best** — and if the underlying claim is verifiable via an upstream T1 document the outlet cites, Phase 2 must follow the citation chain back to that document before finalizing.

### Fix 2: Phase 0.3 Substitution Check — execution, not declaration

The substitution check now requires:
- An explicit *action* ("I have fetched the original document at ly.gov.tw" / "I have grep'd the official press release")
- Not just an *intent* ("I will avoid substituting news summary for the original bill")

If the action wasn't performed, Phase 0.3 outputs an explicit blocker and the run halts until the action is completed.

### Fix 3: Tier downgrade warning in output

When the verification output contains any source that was *almost* labelled T1 but downgraded to T2 because it failed the domain check, the output must include a short note: "原欲引用 X 作為 T1，但屬新聞媒體，已降為 T2，並追溯至 Y。"

This makes the laundering attempt visible to the user, building trust through transparency.

---

## What this case teaches

1. **Rules written into a Skill don't enforce themselves.** Source-Trail v1.1 had a Tier table. I still mis-tiered a source. Rules need enforcement hooks (domain checks, action requirements), not just statements.

2. **Convergent news ≠ convergent sources.** Five UDN articles paraphrasing the same legislative bill are one source, not five.

3. **The user is the last line of defense.** If the user hadn't pushed back on the T1 claim, the wrong table would have been published. Source-Trail's value isn't "Claude doesn't err" — it's "Claude's errors are catchable when the user asks the right question."

4. **Pedagogical opportunity:** A documented failure case is more useful to the Skill than a documented success. Add this case to `examples/` and reference it from the Phase 2 rules.

---

## Search trail

- WebSearch queries: 14 total across two re-run rounds
- WebFetch attempts: 5 (3 successful, 2 returned binary blobs requiring pdftotext extraction)
- Direct PDF downloads via `curl -A '<browser UA>'`: 2
- pdftotext extractions: 2
- Verification date: 2026-05-11

## Sources finally verified at T1

- `ppg.ly.gov.tw/ppg/SittingRelatedDocumentReportMatter/download/agenda1/02/pdf/11/05/09/LCEWA01_110509_00254.pdf` (KMT/Lo bill text)
- `ppg.ly.gov.tw/ppg/bills/202110116630000/details` (TPP bill)
- `ppg.ly.gov.tw/ppg/SittingAttachment/download/2026042459/01142490203230600002.pdf` (committee aggregate of 38 medical-law bills)
- `mohw.gov.tw/cp-6656-77368-1.html` (MOHW ratio standard, 2024-01-26)

## Falsifiability

This case's conclusion would itself be wrong if (a) the official translation/transcription of the floor amendments differs materially from the bill-level T1 documents I located — but no T1 source for the actual passed text exists yet as of 2026-05-11; the legislative gazette for that day was not yet published. The case therefore documents what *can* be verified at T1 and explicitly flags what *cannot* be yet, rather than confidently asserting the final statute text.
