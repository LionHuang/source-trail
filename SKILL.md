---
name: source-trail
version: 1.0.0
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
| Low | 0, 0.5, 2, 3 | 20-40s |
| Medium | + 1?, 4 | 40-60s |
| High | All (0–5) | 60-90s with parallel; 1.5-3min sequential |

Sequential execution can double the time. **Always parallelize independent fetches.** See parallelization rules in Phase 2 and Phase 5.

For users requesting speed: they can say "fast" / "lite" / "skip Phase 5" — degrade gracefully by skipping conditional phases first (5 → 4 → 1), keeping Phase 2 + 3 as the irreducible minimum.

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

**⚡ Parallelization rule:** When fetching multiple independent sources (different candidates, different domains, different aspects), issue all WebFetch / WebSearch calls **in parallel** in a single tool-use batch. Sequential execution wastes wall-clock time for no benefit. Only serialize when one fetch's result determines the next fetch's URL.

Example: Verifying 4 baseball players → 4 parallel WebFetch calls, not 4 sequential.

**The Iron Law applies here**: if the question requires data past your training cutoff, you MUST execute the search, not defer to the user.

### Phase 3: Disclosure Output（揭露式呈現）

#### Output Structure — Inverted Pyramid（倒金字塔）

Order content by **user importance**, not by phase order:

```
1. Conclusion / TL;DR             ← 2-3 sentence direct answer
2. Main content                    ← Structured details
3. Caveats / risks (if applicable) ← Real warnings only
4. Blind spots / additions         ← What might be missed
———— horizontal rule ————
5. Verification details            ← Sources / falsifiability / reproducibility
```

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

Use this to decide which phases to run. Phase 0, 0.5, 2, 3 always run. Others depend on stakes and scope.

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
| Pure factual lookup (Low stakes) | 0, 0.5, 1?, 2, 3 |
| Comparative / evaluative (Medium) | 0, 0.5, 1?, 2, 3, 4 |
| Decision support / harm if wrong (High) | All phases (0–5) |
| News / current events | 0, 0.5, 1?, 2, 3, +5 if politicized |
| Technical specs / docs | 0, 0.5, 2, 3 |
| Medical / financial / legal | All phases (0–5), no shortcuts |

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

---

## Origin

Distilled from a 2026-05-08 working session on epistemic humility in LLM-assisted research. Each rule traces to a documented failure mode caught during testing. See `examples/` for the original cases.
