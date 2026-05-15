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

- **事业 bias**: The engine predicts career as the top domain for ~75% of subjects, but ground truth is only ~27%. The scoring weights need recalibration.
- **感情 blind spot**: Relationship detection is the weakest domain at 2.5% hit rate.
- **健康 and 六亲**: Not captured at all by the current engine. Zero hits.
- **Small dataset**: 90 subjects. This is the only public BaZi QA benchmark, but it's not large.

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
