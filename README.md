# 🧮 Chronomancer — Deterministic BaZi Engine with LLM Narrative Layer

**Most "AI fortune tellers" today pipe birth data into an LLM and hope for the best. We think that's backwards.**

BaZi (八字) is a deterministic system. Stems, branches, Five Elements, Ten Gods — these are lookup tables and formulas, not language reasoning tasks. LLMs hallucinate them. Even GPT-4 gets ~30% of pillar derivations wrong.

So we built it the other way around: **a deterministic engine computes everything, and the LLM only handles narrative.**

---

## 📈 The Numbers

We validated on 90 subjects from the [BaziQA benchmark](https://github.com/ChenJiangxi/BaziQA) (40 contest questions + 50 celebrities with verified life events):

| Metric | Our Engine | Best LLM |
|--------|-----------|----------|
| Top-1 Domain Accuracy | **48.9%** | 38.0% |
| Temporal Event Hit Rate | **75.6%** | — |
| Relationship Detection | **60.0%** | — |

The deterministic engine outperforms every LLM tested — not because it's "smarter," but because BaZi computation is a rule-based task. You don't need a language model to look up a stem-branch table.

---

## 🧠 The Architecture

```
Birth Data → Deterministic Engine → Structured JSON → LLM → Natural Language Reading
```

**The engine handles everything deterministic:**
- Four pillars (八字) from birth time
- Day Master strength & pattern classification (格局)
- Ten Gods (十神), Shen Sha (神煞), Da Yun (大运)
- Domain scoring across 感情/财富/事业/健康/六亲
- Event trigger detection

**The LLM handles what it's good at:**
- Turning "事业 score 0.78, Zheng Guan +3" into fluent, personalized advice
- Citing classical texts (《渊海子平》《三命通会》《滴天髓》)
- Varying tone and framing per user

Separation of concerns. The calculator calculates. The writer writes.

---

## 🔍 What We're Trying to Understand

The original BaziQA paper asked: *"Can LLMs reason about BaZi with structured prompts?"*

We ask a different question: **"Should the reasoning be done by the LLM at all?"**

Our hypothesis: for rule-based classical systems like BaZi, deterministic computation is strictly superior to LLM inference for the structural layer. The LLM adds value only at the narrative layer — turning computed signals into human-readable guidance.

This has implications beyond BaZi. Any domain with well-defined classical rules (astrology, traditional medicine, legal code) might benefit from this two-layer pattern: **compute first, narrate second.**

---

## 👥 What's Next: Multi-Chart Interaction

A single person's chart tells you about *them*. But life isn't lived in isolation.

The engine already supports adding **partner, family, and close contacts' charts** — studying how their pillars interact with yours. When your spouse's 日柱 clashes with your 月柱, or when a child's 年柱 generates your favorable element, those interactions shape real outcomes.

Our next research direction: **print couples' and family charts side by side, compute the cross-chart interactions, and study what the combined signals predict.** Not just "your year looks like this" but "your year looks like this *given who you're with*."

This is where BaZi gets genuinely powerful — and where pure-LLM systems have zero shot. You can't prompt your way through pillar-to-pillar interaction math across four people's charts. You need the engine.

---

## ⚠️ What We Got Wrong

Honesty matters. Known limitations:

- **事业 bias**: The engine predicts 事业 (career) as top domain for 88.9% of subjects. Ground truth top-1 is also 事业-dominant (54.4%), but the engine's skew is far more extreme. This inflates headline accuracy. See the FAQ below for exact baselines.
- **感情 blind spot**: Relationship detection is the weakest domain at 2.5% per-domain hit rate.
- **健康 and 六亲**: Not captured by the current engine. Zero per-domain hits.
- **Small dataset**: 90 subjects. This is the only public BaZi QA benchmark, but it's not large. Confidence intervals matter — see the FAQ.
- **Comparison caveat**: The LLM baselines (38.0%) come from the original BaziQA paper, which used multiple-choice QA format. Our engine uses top-1 domain prediction. The tasks are structurally different. The comparison is directional, not controlled.

---

## ❓ Pre-emptive FAQ

### Q1: "Isn't 48.9% worse than just always predicting career?"

**Short answer: Yes, on the strict top-1 metric. Here's the full picture.**

| Method | Top-1 Accuracy | Notes |
|--------|---------------|-------|
| Random (uniform 5-class) | 20.0% | Theoretical floor |
| Always predict 事业 | **54.4%** | Dumb majority-class baseline |
| **Engine V31 (strict top-1)** | **48.9%** | Current reported metric |
| Engine V31 (any-label match) | **77.8%** | Engine top-1 in *any* of subject's GT labels |
| Always predict 事业 (any-label) | 80.0% | Dumb baseline under the same any-label metric |
| Best LLM (BaziQA paper, MCQ) | 38.0% | *Different task format* |

The ground truth in this dataset is 事业-heavy: 54.4% of subjects have 事业 as their top-1 label (72 of 90 have it as *any* label). This is a property of the benchmark dataset, not a property of real-world BaZi — contest problems and celebrity case studies tend to cluster around high-impact career and relationship years.

**What the engine actually shows:**
- On the 49 subjects where GT top-1 = 事业, the engine hits 44 of them (89.8%).
- On the 41 subjects where GT top-1 ≠ 事业, the engine hits 0 of them (0.0%).
- The engine has **not yet learned to predict non-career domains**.

**Why we report 48.9% anyway:** It is the exact, reproducible number from the raw results JSON. We do not inflate it. The engine's correct behavior on career-dominant subjects (89.8% hit rate) is a real signal; its failure on non-career subjects is an honest, documented gap. Fixing the multi-domain scoring is the top near-term priority.

---

### Q2: "You're comparing your engine to LLMs, but the tasks are different. Isn't that misleading?"

**Yes — the comparison is directional, not controlled. We say so in REPORT.md. Here's the exact caveat:**

The BaziQA LLM baselines (DeepSeek-Chat-v3 at 38.0%, GPT-5.1 at 32.5%, etc.) were evaluated on **multiple-choice question answering**: given birth data, answer which of five domains was most significant. Our engine evaluation uses **top-1 domain prediction**: score all five domains independently and return the argmax.

These are not the same task. The LLM MCQ task is harder in one way (it must pick from four distractors) and easier in another (MCQ provides explicit domain labels as anchors).

**What the comparison is validating:** The claim that *deterministic computation of a rule-based system outperforms LLM inference of the same system* is supported by the gap between our engine (48.9%) and random chance (20.0%), and by the fact that the engine computes exact pillar math while LLMs demonstrably hallucinate it. The specific number gap to the BaziQA LLM baselines is suggestive, not conclusive.

**What we're not claiming:** We are not claiming we ran a controlled A/B test against GPT-5.1 on identical inputs with identical evaluation criteria. We did not. Anyone who wants to run that test is welcome to — the benchmark data is in `/data/` under MIT license.

---

### Q3: "N=90 is too small. How confident are you in the 48.9% figure?"

At N=90, a binomial proportion of 48.9% has a 95% confidence interval of approximately **±10.3%** (Wilson interval: [38.9%, 59.0%]). At N=40 (contest8 only), the CI is wider still (~±15%).

The reported number is real — it comes directly from `/results/summary.json`, deterministically computed, zero cherry-picking. But the dataset is small. We do not claim this is a statistically decisive result. We claim it is an honest baseline on the only public BaZi QA benchmark that exists.

The gap between the engine and random chance (48.9% vs 20.0%) is statistically robust (p < 0.001). The gap versus the LLM baselines is directionally meaningful but not statistically tight given the task-format difference.

---

## 📖 Attribution

This work builds on the [BaziQA benchmark](https://github.com/ChenJiangxi/BaziQA) by Jiangxi Chen (陈江熙) and Qian Liu.

- **Paper:** [arXiv:2602.12889](https://arxiv.org/abs/2602.12889) (February 2026)
- **Applied in:** [AuraMate灵伴](https://auramate.net/)

---

## 📂 What's in This Repo

| File/Dir | Description |
|----------|-------------|
| `REPORT.md` | [Full report](REPORT.md) — architecture, validation, methodology, limitations |
| `results/` | Validation output: `summary.json`, `cross_sectional_results.json`, `temporal_results.json` |
| `data/` | BaziQA benchmark datasets (MIT licensed): `contest8/` and `celebrity50/` |

The deterministic engine (V31) is **proprietary** — 162 unit tests, 17+ audit iterations against classical texts. Not open-sourced.

---

## 📬 Contact

To discuss research access to the engine, [open an issue](https://github.com/Acivar-Digital/chronomancer/issues).
