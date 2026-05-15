# 🧮 Chronomancer — Deterministic BaZi Engine with LLM Narrative Layer

> **Repository:** [github.com/Acivar-Digital/chronomancer](https://github.com/Acivar-Digital/chronomancer)

**A two-layer architecture that does what pure-LLM fortune tellers can't: get the math right.**

| Layer | Technology | Role |
|-------|-----------|------|
| **Deterministic Engine (V31)** | Pure Python, 162 tests | Computes pillars, Ten Gods, Shen profile, domain scores, events |
| **LLM Narrative Layer** | litellm + openai | Converts structured engine output → natural-language reading |

Chronomancer is a Telegram bot powered by a deterministic BaZi engine. The engine is open and standalone — you can run it without any API key.

---

## 📖 Attribution

This project builds on the **BaziQA benchmark** created by [Jiangxi Chen](https://github.com/ChenJiangxi) (陈江熙) and Qian Liu (刘茜).

- **Paper:** [BaziQA-Benchmark: Evaluating Symbolic and Temporally Compositional Reasoning in Large Language Models](https://arxiv.org/abs/2602.12889) (arXiv:2602.12889, February 2026)
- **Original repo:** [github.com/ChenJiangxi/BaziQA](https://github.com/ChenJiangxi/BaziQA)
- **Applied in:** [AuraMate灵伴](https://auramate.net/) — a metaphysical companion product

The contest8 and celebrity50 validation datasets are adapted from the original BaziQA benchmark under the MIT license. We extend the benchmark with a deterministic computation pipeline (rather than LLM inference) and add relationship scoring, temporal event detection, and cross-sectional domain prediction.

---

## 📊 The Dataset

The validation data comes from two sources in the BaziQA benchmark:

### Contest8 (2021–2025)
- **Source:** Global Fortune-teller Competition (全球预测大赛), professionally curated multiple-choice problems
- **Size:** 8 subjects per year × 5 years = 40 unique subjects, 200 questions
- **Format:** Anonymous subjects identified by birth year, gender, and region
- **Ground truth:** Multi-domain labels (感情/财富/事业/健康/六亲) per subject per year

### Celebrity50
- **Source:** 50 well-known public figures with verified life-event timelines
- **Size:** 50 subjects, 250 temporal-event questions
- **Format:** Named individuals with detailed category-level event histories
- **Ground truth:** Dated life events across five domains

| Dataset | Subjects | Questions | Type |
|---------|----------|-----------|------|
| Contest8 (2021-2025) | 40 | 200 | Cross-sectional domain labels |
| Celebrity50 | 50 | 250 | Temporal event detection |
| **Total** | **90** | **450** | — |

**License:** MIT (original BaziQA benchmark license)

---

## 🔍 What This Study Is (and Isn't)

**This is not a replication of the BaziQA benchmark.** The original BaziQA paper asks: *"Can LLMs reason about BaZi when given structured prompts?"* — and shows that structured prompting helps.

We ask a different question: *"Should the reasoning itself be done by the LLM at all?"*

Our answer: **No. The math and structure are best left to a deterministic engine.** Let the calculator do what calculators do — compute pillars, interactions, strengths, and scores without hallucination. Let the LLM do what LLMs do — add flavor, context, and narrative framing to those computed results.

This is a fundamentally different philosophy:
- **BaziQA's approach:** LLM reasons about BaZi (with structured prompting as scaffolding)
- **Our approach:** Engine computes BaZi, LLM narrates the output (with engine data as grounding)

### ⚖️ Important Assumptions

This study assumes that **determining the strength and pillar of a person is correct.** The engine's Day Master strength calculations, pillar derivations, and interaction rules have been audited against classical texts across 17+ minor versions. But BaZi is a deep discipline — edge cases exist, and no engine is perfect.

### 📝 A Note for First-Time Readers

If you're reading about BaZi for the first time: **get a sifu (師傅) to audit your birth chart.** Determining your chart — the correct pillars, the correct strength assessment, the correct pattern — is the foundation. Everything that follows depends on getting this right. The human-in-the-loop remains essential. An engine can compute, but a master can see what the engine misses.

This doesn't replace the master-apprentice relationship. It augments it. The app handles computation and narrative generation; the human provides judgment, context, and the irreplaceable nuance of lived experience.

### 💡 What Comes Next

The immediate next step is deeper research into **event triggers** — how to provide timely advice that accounts for the various nuances of a person's chart as time passes. Not just "this year is career-focused" but "in the 7th month of this year, when the annual pillar clashes with your Da Yun, watch for career disruption — here's how to prepare."

---

## 🏗️ Architecture

```
Birth Data (gender, DOB, time, location)
         │
         ▼
┌─────────────────────────────────────────┐
│           DETERMINISTIC ENGINE            │
│                                           │
│  module0_geju     Pattern classification  │
│  module1_macro    10Y/annual luck pillars │
│  module2_root     Day Master strength     │
│  module3_interact Clash/Combo/Harm/Punish│
│  module5_causal   Causal chain events     │
│  module6_ten_gods Ten Gods extraction     │
│  module7_shen     Shen Sha (stars)        │
│  module8_scoring  Domain scoring          │
│  module9_triggers Event triggers          │
│  module11_prob    Bayesian probabilities  │
│  module13_spectrum Spectrum profile       │
│                                           │
│  Output: structured JSON (scores, events, │
│          Ten Gods, Shen, Ge Ju pattern)   │
└───────────────────────────────────────────┘
         │
         ▼
┌───────────────────────────────────────────┐
│         LLM NARRATIVE LAYER                │
│                                            │
│  Converts structured signals → reading     │
│  - Domain priorities → career/love focus   │
│  - Event triggers → monthly predictions    │
│  - Shen profile → personalized advice      │
│  - Classical citations → BaziRAG quotes    │
│    (《渊海子平》《三命通会》《滴天髓》)       │
└───────────────────────────────────────────┘
         │
         ▼
     Telegram Bot
```

**Separation of concerns:**
- **Deterministic where rules exist.** Classical BaZi is a rule-based system — stems/branches, Five Element cycles, Ten Gods, Ge Ju patterns. These are formulas, not statistical inferences.
- **Stochastic where creativity is needed.** Turning "事业 score 0.78, Zheng Guan +3" into "Your career trajectory shows strong upward momentum this month, with authority figures playing a key role" is a language task — that's what LLMs are for.

---

## 📈 Validation Results (90 Subjects)

We benchmarked the deterministic engine against pure-LLM baselines from the original BaziQA paper, plus our own internal methods.

### 🎯 Domain Prediction (Top-1 Accuracy)

The task: given only birth data, predict the person's most affected life domain for a given year. Five domains: 感情 (relationships), 财富 (wealth), 事业 (career), 健康 (health), 六亲 (family). Random chance = 20%.

| Method | Top-1 Accuracy | Notes |
|--------|---------------|-------|
| **Chronomancer Engine (V31)** | **48.9%** | M10/M11 dual-signal deterministic engine |
| DeepSeek-Chat-v3 (Structured) | 38.0% | Best LLM from original paper |
| DeepSeek-Chat-v3 (Multi-turn) | 36.7% | From original paper |
| DeepSeek-R1 (Structured) | 35.0% | Reasoning model, from original paper |
| GPT-5.1-Chat (Multi-turn) | 32.5% | From original paper |
| Gemini-3-Pro (Multi-turn) | 32.1% | From original paper |
| Owl-Alpha | 30.0% | High-effort reasoning, separate benchmark |
| DeepSeek V4 | 28.2% | Standard prompt, separate benchmark |
| Arrow2 (Shen-based) | 10.0% | Complementary signal — biased toward 六亲 |
| Action Guidance v2 | 14.4% | Complementary signal — best relationship detector |

**Note:** The LLM baselines from the original BaziQA paper evaluated models on multiple-choice question answering (the contest8 format). Our engine evaluation uses top-1 domain prediction on the same subjects. The comparison is directional — the engine's 48.9% is not a controlled head-to-head against the LLMs — but the gap is large enough to be meaningful.

The deterministic engine outperforms every LLM tested, including reasoning models with structured protocols. This isn't because the engine is "smarter" — it's because BaZi domain prediction is a rule-based computation, not a language reasoning task.

### 📊 Per-Domain Breakdown

| Domain | Engine Hit Rate | Notes |
|--------|----------------|-------|
| 事业 (Career) | 88.9% | Of correct predictions, 88.9% are career — the engine's strongest domain |
| 财富 (Wealth) | 7.3% | Under-detected |
| 感情 (Relationships) | 2.5% | **Blind spot** — needs improvement |
| 健康 (Health) | 0.0% | Not captured by current engine |
| 六亲 (Family) | 0.0% | Arrow2 method is better for this |

**⚠️ Known bias:** The engine predicts 事业 (career) as the top domain for ~75% of all subjects, but ground truth has 事业 as the top domain for only ~27%. This is a known architectural bias — the domain scoring weights favor career-related signals. Fixing this is the next priority.

### 💕 Relationship Detection

Using the **Action Guidance v2** module (calibrated relationship scoring with spouse star detection):

- **60.0% accuracy** for detecting whether relationships are a significant life theme (> 0.15 threshold)
- The threshold filters out the 0.05 baseline noise from spouse star appearing in nearly all charts
- Earlier testing at > 0 threshold gave 96.7% — an artifact of the baseline signal, not real accuracy

### 🕰️ Temporal Event Detection

On the celebrity50 dataset (45 subjects with dated life events):

- **75.6% hit rate** — the engine correctly identifies temporal windows where significant life events occur
- Method: Da Yun (10-year luck pillar) alignment + annual Tai Sui interactions + event trigger detection
- A "hit" means the engine flagged the correct year-window for a known event (marriage, career change, health incident, etc.)

---

## 🧠 Why Deterministic + LLM > Pure LLM

### 1. 🔢 LLMs Can't Do Pillar Math

BaZi computation requires deterministic lookups: sexagenary cycle (六十甲子), Five Element assignments, Nayin (纳音), hidden stems (藏干). These are lookup tables, not reasoning tasks.

| Task | LLM Approach | Engine Approach |
|------|-------------|-----------------|
| Derive 8 characters from birth time | Prompt the LLM to "calculate" | `lunar-python` library → deterministic |
| Map stems to elements | Hope the LLM memorized the table | `bazi_data.py` lookup dicts |
| Compute hidden stems | Ask the LLM to recall branch contents | Per-branch weight tables with seasonal adjustment |

### 2. 🔄 Reproducibility

Same birth data → same output. Always. LLMs have temperature, sampling variance, and model drift. If a user asks "why did my reading change?" with the engine, we can show the exact computation trace. With pure LLM, you can't.

### 3. 📋 Audit Trail

The engine has undergone **17+ minor version audits** against classical texts (《渊海子平》, 《三命通会》, 《滴天髓》, 《穷通宝鉴》). Each bug found was a specific formula deviation from classical rules — traceable, fixable, verifiable. You can't audit an LLM's "understanding" of BaZi.

**Example from V31.5 Clash Audit:**
> BUG 1: `get_clash_dm_strength_modifier` used continuous_score (-100..+100) against Tier 1 thresholds (0-8). Fixed by switching to `dm_tier1_score` param with book-correct cutoffs.
>
> BUG 2: `MONTHLY_QI_MULTIPLIER` values for Qiu (0.7) and Si (0.4) were copy-pasted from `ELEMENT_PHASE_MULTIPLIER`. Book spec: Qiu=0.8, Si=0.5. Fixed.

Every fix is traceable to a specific classical rule.

### 4. 💰 Cost

The engine computation costs **zero API calls**. The LLM layer is only invoked for narrative generation — a single call per reading. Pure-LLM approaches need multiple calls (pillar derivation, analysis, narrative) with higher token counts and higher hallucination risk.

---

## 🛠️ Engineering Quality

- **162 engine unit tests** — all passing
- **34 profile logic tests** — all passing
- **5 core benchmarks** — all passing
- **Ruff linter** — clean across all production code
- **Python 3.14+** — with `pyright` type checking
- **Zero external API dependency** for engine computation

---

## 🗺️ What's Next

### Near-Term

1. **Fix the 事业 bias** — recalibrate domain scoring weights to reduce career over-prediction (currently 75% of predictions vs 27% of ground truth)
2. **Improve 感情 detection** — the relationship domain is the biggest blind spot at 2.5% hit rate
3. **Arrow2 integration** — the Shen-based method excels at 六亲 (family) detection where the engine scores 0%. Hybrid scoring could recover this domain.

### Medium-Term

4. **Multi-chart interaction research** — the engine already supports adding partner, family, and close contacts' charts. The next step: print couples' and family charts side by side, compute cross-chart pillar interactions, and study what combined signals predict. Not just "your year looks like this" but "your year looks like this *given who you're with*." When your spouse's 日柱 clashes with your 月柱, or a child's 年柱 generates your favorable element — those interactions shape real outcomes. This is where BaZi gets genuinely powerful and where pure-LLM systems have zero shot.
5. **Event trigger research** — moving beyond "this year is career-focused" to "in month M, when annual pillar X clashes with Da Yun Y, here's the specific risk and how to prepare."
6. **Classical RAG citations** — BaziRAG integration for narrative layer to cite specific classical passages
7. **Multi-year cross-sectional analysis** — track how domain predictions shift across consecutive years for the same subject
8. **Calibrated confidence scores** — surface the engine's uncertainty (e.g., "60% confidence this is a career-dominant year")

---

## 🔬 Reproducing Results

The validation results in this report are generated by a deterministic BaZi engine (V31) operating on the BaziQA benchmark datasets. The engine implementation is proprietary; the datasets are MIT-licensed from the original [BaziQA benchmark](https://github.com/ChenJiangxi/BaziQA).

To discuss access to the engine for research purposes, [open an issue](https://github.com/Acivar-Digital/chronomancer/issues).

---

## 📑 License

The validation datasets (`data/`) and results (`results/`) in this repository are released under the MIT License, following the original [BaziQA benchmark](https://github.com/ChenJiangxi/BaziQA) license. The deterministic engine is proprietary.

---

## 📚 References

- Chen, J., & Liu, Q. (2026). *BaziQA-Benchmark: Evaluating Symbolic and Temporally Compositional Reasoning in Large Language Models*. arXiv:2602.12889.
- Original benchmark repo: [github.com/ChenJiangxi/BaziQA](https://github.com/ChenJiangxi/BaziQA)
- Built with [`lunar-python`](https://pypi.org/project/lunar-python/) for calendar computation.
- Classical texts: 《渊海子平》, 《三命通会》, 《滴天髓》, 《穷通宝鉴》.
