# Chronomancer — Public Validation Report

This repository contains the validation report and results for **Chronomancer**, a deterministic BaZi engine with an LLM narrative layer.

## What's Here

| File/Dir | Description |
|----------|-------------|
| `REPORT.md` | Full report: architecture, validation results, comparison with LLM baselines |
| `results/` | Validation output: `summary.json`, `cross_sectional_results.json`, `temporal_results.json` |
| `data/` | BaziQA benchmark datasets (MIT licensed): `contest8/` (2021-2025) and `celebrity50/` |

## Key Results

| Metric | Engine | Best LLM (DeepSeek-Chat-v3) |
|--------|--------|-----------------------------|
| Top-1 Accuracy | **48.9%** | 38.0% |
| Temporal Hit Rate | **75.6%** | — |
| Relationship Detection | **60.0%** | — |

On 90 subjects (40 contest8 + 50 celebrity50), the deterministic engine outperforms every LLM tested in the original BaziQA paper.

## The Engine

The deterministic BaZi engine (V31) is **proprietary**. It computes:

- Four pillars (八字) from birth data
- Day Master strength and pattern classification (格局)
- Ten Gods (十神) and Shen Sha (神煞)
- Da Yun (大运) and annual luck pillars
- Domain scoring across 感情/财富/事业/健康/六亲
- Event trigger detection

The engine is not open-sourced. The LLM narrative layer (Telegram bot, prompt engineering, BaziRAG integration) is also proprietary.

## Datasets

The `data/` directory contains the original BaziQA benchmark datasets:

- **contest8** (2021-2025): 40 subjects, 200 questions from the Global Fortune-teller Competition
- **celebrity50**: 50 public figures with verified life-event timelines

These are adapted from the [BaziQA benchmark](https://github.com/ChenJiangxi/BaziQA) by Jiangxi Chen (陈江熙) and Qian Liu, used under the MIT license.

## Attribution

- **BaziQA Paper:** Chen, J., & Liu, Q. (2026). *BaziQA-Benchmark: Evaluating Symbolic and Temporally Compositional Reasoning in Large Language Models*. arXiv:2602.12889.
- **Original repo:** [github.com/ChenJiangxi/BaziQA](https://github.com/ChenJiangxi/BaziQA)
- **Applied in:** [AuraMate灵伴](https://auramate.net/)

## License

Datasets and results: MIT License (following original BaziQA benchmark license).
Engine: Proprietary.

## Contact

To discuss research access to the engine, [open an issue](https://github.com/Acivar-Digital/chronomancer/issues).
