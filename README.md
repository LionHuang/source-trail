# Source-Trail Skill 溯源查證

> **"An answer without a trail is a guess in formal clothes."**
>
> **「沒有來源軌跡的答覆，是穿著正裝的猜測。」**

---

## What is this? / 這是什麼？

An [Agent Skill](https://agentskills.io/) that forces Claude to **trace every factual claim back to a primary source** — and to disclose what's verified, what's uncertain, and what's a guess.

一個 Agent Skill，強制 Claude 把每一個事實宣稱**上溯到原始來源**，並揭露什麼已驗證、什麼不確定、什麼是猜測。

Unlike asking AI a factual question and getting a confident-sounding answer, this skill runs a structured verification workflow that classifies sources, fetches primary documents, labels confidence levels, and adversarially checks the conclusion before answering.

不同於問 AI 一個事實問題然後拿到一個聽起來很有自信的答案，這個 Skill 會執行結構化的查證流程：來源分級、抓取原始文件、標註信心等級、敵對審查結論，然後才回答。

## What it does / 這個 Skill 做什麼

When triggered on a factual question, the skill walks through a 6-phase verification workflow:

當在事實性問題上被觸發時，Skill 會走完 6 個查證階段：

| Phase | Action / 動作 |
|-------|--------|
| **0** | Trigger check — is this factual? / 觸發判斷：這是事實性問題嗎 |
| **0.5** | Pre-register search strategy / 預先登記搜尋策略（防止事後挑詞）|
| **1** | Scope reconnaissance / 範圍偵察（Gemini 偵察兵 or broad WebSearch）|
| **2** | T1/T2 verification via WebFetch / 抓取原始來源驗證（必做）|
| **3** | Disclosure output / 揭露式呈現（信心標籤 + 來源紀錄）|
| **4** | Peer review / 同儕審查 |
| **5** | Devil's advocacy — actively search counter-evidence / 敵對審查 — 主動找反證 |

Phases 1, 4, 5 are conditional on stakes level. Phases 0, 0.5, 2, 3 always run.

Phase 1、4、5 視風險等級條件執行。Phase 0、0.5、2、3 永遠執行。

## The Source Tier System / 來源分級系統

Every source is classified before use. Factual claims must trace to T1 or T2.

每個來源使用前先分級。事實宣稱必須能上溯到 T1 或 T2。

| Tier | Type | Examples |
|------|------|----------|
| **T1** | Primary / Original 原始來源 | Official documents, raw data, party-of-record statements |
| **T2** | Authoritative Secondary 權威次級 | Reuters, AP, peer-reviewed journals |
| **T3** | Reported with citation 帶引用報導 | News articles citing primary sources |
| **T4** | Aggregated summary 聚合摘要 | Wikipedia, summary sites |
| **T5** | LLM-synthesized 大模型合成 | Claude / Gemini / GPT outputs — **not evidence** |

**LLM agreement is not verification.** Two models trained on similar corpora reaching the same conclusion adds little epistemic value.

**LLM 之間的同意不是驗證。** 兩個在類似語料上訓練的模型得出同一結論，認知價值很低。

## The Iron Law / 鐵律

> **Knowledge cutoff is a tool-call signal, not a give-up clause.**
>
> **知識截止不是放棄的出口，是觸發工具的訊號。**

When the model detects a time point past its training cutoff, the correct action is to **call WebSearch / WebFetch immediately** — not to disclaim and recommend the user check elsewhere. This is the single rule that does the most work.

當模型偵測到訓練截止之後的時間點，正確動作是**立刻呼叫 WebSearch / WebFetch**——而不是聲明限制然後叫使用者自己去查。這是整個 Skill 中影響力最大的單一規則。

## Theoretical Foundation / 理論基礎

This framework is a practical synthesis of established source-evaluation methodologies:

本框架整合三個學科的成熟方法論：

- **Source Criticism (Quellenkritik) — Leopold von Ranke, 19th-century historiography** — Foundational method requiring every historical claim to trace back to primary documents. The T1-T5 tier system is its digital-age extension.
- **來源批判（Quellenkritik）— Ranke 19 世紀史學方法**：要求每個歷史宣稱都必須能上溯到原始檔案。本 Skill 的 T1-T5 分級是其數位時代延伸。

- **Admiralty Code / NATO Source Reliability Rating** — Military intelligence's two-axis system (A-F source reliability, 1-6 information credibility). The direct ancestor of T1-T5 classification.
- **Admiralty Code / NATO 情報界源可靠度評級**：A-F 來源可靠度與 1-6 資訊可信度雙軸系統，是 T1-T5 的直系祖先。

- **Estimative Language (US Intelligence Community Directive 203)** — Calibrated confidence labels (Verified / Likely / Possible / Speculation) used in intelligence reports to prevent confidence inflation.
- **Estimative Language（美國情報總監辦公室 ICD 203）**：已驗證 / 高信心 / 中信心 / 低信心 / 不採信的標籤系統，源自美國情報界用以避免信心高估的標準。

## Installation / 安裝

### Claude Code

```bash
git clone https://github.com/LionHuang/source-trail.git ~/.claude/skills/source-trail
```

Restart Claude Code (or start a new session) for the skill to load.

重啟 Claude Code（或開新 session）讓 Skill 載入。

### Claude.ai (Web / Mobile)

1. Download this repository as a ZIP (Code → Download ZIP)
2. Go to **Settings → Features → Custom Skills**
3. Upload the ZIP file
4. The skill activates automatically on factual questions — works on mobile too

1. 下載本 repo 為 ZIP（Code → Download ZIP）
2. 進入 **Settings → Features → Custom Skills**
3. 上傳 ZIP
4. Skill 會在事實性問題出現時自動啟動——手機 App 也能用

### Other Platforms (Cursor, Codex, etc.)

Copy the `SKILL.md` and `examples/` folder to your platform's skill directory. The Agent Skills format is compatible with 20+ coding agents.

複製 `SKILL.md` 和 `examples/` 資料夾到對應平台的 skill 目錄。Agent Skills 格式相容於 20+ 個 coding agents。

### Optional: Gemini CLI integration / 可選：Gemini CLI 整合

For Phase 1 (scope reconnaissance) and Phase 4 (peer review) enhancements:

```bash
npm install -g @google/gemini-cli
gemini auth
```

If Gemini is not installed, the skill gracefully degrades:
- Phase 1 → uses broad WebSearch instead of Gemini scout
- Phase 4 → uses Claude self-roleplay instead of Gemini peer review

未安裝 Gemini 時，Skill 會優雅降級：Phase 1 改用 broad WebSearch，Phase 4 改用 Claude 自我角色扮演批評者。

## Usage / 使用方式

The skill auto-triggers on factual questions matching its description. You can also invoke manually with `/source-trail`.

Skill 會在符合描述的事實問題上自動觸發，也可手動 `/source-trail` 呼叫。

**Examples / 範例：**

```
2026 年台積電最新一季營收是多少？

What's OpenAI's latest model release this month?

Faker 現在還是韓國 LCK 最強的中路選手嗎？

Which Python ORM has the highest GitHub stars right now?
```

### Sample Output / 範例輸出

```
🔍 /source-trail activating
   Stakes: Low
   Expected: 2-3 searches

**2026 Q1：台積電淨收入 NT$1.13 兆，YoY +35%。**
毛利率 66.2%（歷史新高），每股淨利 $18.16 USD。

---
## 查證細節

**來源**: ir.tsmc.com/investor/quarterly-results (T1, 2026-04-17)
**反證條件**: 台積電若重述財報，此答案需更新。
**搜尋紀錄**: "TSMC 2026 Q1 quarterly results" / 2026-05-08
```

Verified facts are presented inline without labels (default = verified). Only non-verified exceptions carry ⚠ warnings. Phase numbers are never shown to the user.

已驗證事實直接內文呈現，不加標籤（預設 = 已驗證）。只有非驗證的例外會帶 ⚠ 警示。階段編號永遠不暴露給使用者。

## Real-World Cases / 實戰案例

The `examples/` directory documents the failure modes that motivated each rule:

`examples/` 目錄記錄了催生每條規則的失敗案例：

- **[`case-tsmc.md`](examples/case-tsmc.md)** — Before/after the Iron Law: same skill structure produced "please check yourself" before, vs. actually fetching T1 data after. The clearest demonstration of the skill's net value.
- **[`case-baseball.md`](examples/case-baseball.md)** — Phase 1 Gemini scouting succeeds where solo WebSearch fails (2026 Taiwanese MLB players, otherwise missed entirely).
- **[`case-faker.md`](examples/case-faker.md)** — Phase 4 peer review's double-edged nature: Gemini's methodological critique is valid AND fabricates its own specifics.

## Origin Story / 起源

I use Claude as a research assistant. One day I asked it which Taiwanese MLB players had played the most games in 2026 — it confidently synthesized a wrong answer from outdated search snippets. Then I asked about TSMC's latest quarterly revenue — it bailed out with "my training data is from 2025, please check the IR website yourself." Then I asked if Faker was still Korea's strongest mid-laner — it answered half from training data and pushed the rest onto me.

我把 Claude 當研究助手用。有一天我問它 2026 年出賽最多的台籍 MLB 球員——它從過時的搜尋摘要合成了一個錯誤答案，還煞有介事地給我。我又問台積電最新一季營收——它說「訓練資料截止在 2025，建議你自己上 IR 網站查」就放棄了。我再問 Faker 現在還是韓國最強的中路選手嗎——它一半用訓練資料回答，一半把工作丟回給我。

These three failures are different surface symptoms of the same root cause: **LLMs do not have epistemic discipline by default.** They can call tools, but they don't always know when they should — they default to "answer from what I know" until you force them otherwise.

這三個失敗是同一個根本原因的不同表面症狀：**LLM 預設沒有認識上的紀律。** 它們會用工具，但不一定知道什麼時候該用——預設行為是「用知道的回答」，除非強制要求。

So I synthesized methodologies from journalism (multi-source verification), research ethics (pre-registration, peer review), intelligence tradecraft (estimative language, devil's advocacy), and evidentiary standards (chain of custody) into one skill that turns this discipline into Claude's default behavior on factual questions.

於是我把新聞工作（多重來源驗證）、研究倫理（預先登記、同儕審查）、情報分析（信心標籤、敵對審查）、法庭證據（證據鏈）這四個領域的方法論整合成一個 Skill，把這種紀律變成 Claude 在事實性問題上的預設行為。

This is the skill I wished Claude had by default.

這就是我希望 Claude 預設就有的 Skill。

## Contributing / 貢獻

Contributions welcome. The skill evolves based on observed failure modes:

歡迎貢獻。Skill 隨觀察到的失敗模式演進：

1. Document a failure case in `examples/` (skill triggered but produced wrong/unsafe output)
2. Propose a rule update in `SKILL.md` that would have prevented it
3. Open a PR with both

When proposing rules, prefer **relative descriptions over absolute values** (e.g., "knowledge cutoff" not "August 2025"). Rules with hardcoded dates expire silently.

提議規則時，優先使用**相對描述而非絕對數值**（例如用「知識截止」而非「2025 年 8 月」）。寫死日期的規則會無聲過期。

## License

MIT — see `LICENSE`.

---

*Built by [Lion Huang](https://github.com/LionHuang) — where information rigor meets AI-augmented research.*

*由 Lion Huang 出品——資訊嚴謹度與 AI 輔助研究的交會點。*
