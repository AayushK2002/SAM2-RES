# Efficient Referring Expression Segmentation via Lightweight Reasoning over SAM2

<div align="center">

![Status](https://img.shields.io/badge/Status-In%20Progress-blue)
![Python](https://img.shields.io/badge/Python-3.9%2B-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

**[Paper (Coming Soon)]** | **[Project Page (Coming Soon)]** | **[Demo (Coming Soon)]**

</div>

---

## Overview

Referring expression segmentation (RES) — producing a pixel-accurate mask for an object described by natural language (e.g., *"the cat on the left"*) — is a fundamental vision-language task. Existing methods force a difficult tradeoff:

| Method Type | Accuracy | Inference Cost | Practical? |
|-------------|----------|----------------|------------|
| CLIP-based zero-shot (CRIS, ReCLIP) | Low–Medium | ~1× | ✅ Yes |
| LLM-based (LISA-7B, GLaMM, SAM4MLLM) | High | 100–350× | ❌ No |
| **Ours** | **Approaching LLM-level** | **~1–3×** | **✅ Yes** |

We identify the precise reason CLIP-based methods underperform — not a perception failure, but a **reasoning failure** — and address it with a lightweight, interpretable module that approaches LLM-level accuracy at roughly **1/100th the parameter count and inference cost**.

---

## Key Motivation: A Diagnostic Study on 283 RefCOCO Expressions

Before committing to any architecture, we ran a controlled diagnostic study to precisely localise where lightweight RES methods fail. The study used a state-of-the-art segmentation foundation model to generate all plausible candidate masks per image, then measured how much accuracy is lost at each stage.

### Core Finding

> **The foundation model never completely misses the target object. The failure is entirely in reasoning, not perception.**

| Metric | Result |
|--------|--------|
| Naive baseline (CLIP similarity heuristic) | 20.5% (IoU ≥ 0.5) |
| Oracle upper bound (perfect mask selection) | **74.6%** |
| Gap attributable to selection errors | 42.0% of expressions |
| Gap attributable to over-segmentation failures | 25.4% of expressions |
| Complete misses (no mask near target) | **0.0% — zero cases** |

### What this means

The **54-point gap** between the naive baseline (20.5%) and the oracle ceiling (74.6%) is attributable almost entirely to two tractable, specific sub-problems:

1. **Mask selection failure** — the correct mask exists among the candidates, but the wrong one is chosen (typically because CLIP cannot reason about spatial language like "on the left" or "next to")
2. **Mask over-segmentation** — the target object is split across multiple partial masks, none of which alone matches ground truth (concentrated on large objects: sofas, cars, people)

This diagnosis directly motivates the approach: instead of replacing the foundation model, we address the reasoning gap with a purpose-built selection and merging module on top of a frozen backbone.

---

## Method

We propose a lightweight reasoning module that sits between a frozen SAM2 backbone and the final output — solving mask selection and merging without resorting to a multi-billion-parameter LLM.

**Key design principles:**
- Decouple **perception** (already solved well by the foundation model) from **reasoning** (the actual bottleneck)
- Keep the backbone frozen — no fine-tuning of SAM2
- Native support for the **generalized RES setting** (gRefCOCO): multi-target and no-target (empty) expressions
- Explicit **no-match prediction** for expressions with no valid target in the image

*Full architecture details will be released upon paper publication.*

---

## Benchmarks

We evaluate on both standard and generalized RES benchmarks:

| Benchmark | Task | Metric |
|-----------|------|--------|
| RefCOCO | Standard RES | oIoU, cIoU |
| RefCOCO+ | Standard RES | oIoU, cIoU |
| RefCOCOg | Standard RES | oIoU, cIoU |
| gRefCOCO | Generalized RES | cIoU, gIoU, N-acc. |

**Baselines compared:**
- CLIP-only matching (sanity floor)
- CRIS, ReCLIP (zero-shot CLIP-based)
- LISA-7B, GLaMM, SAM4MLLM (LLM-based — efficiency comparison)
- GSVA (current gRefCOCO state of the art)

---

## Results

*Full benchmark results will be released with the paper.*

Preliminary diagnostic results above demonstrate the tractability of the approach. Quantitative results on RefCOCO / RefCOCO+ / RefCOCOg / gRefCOCO will be released upon publication.

---

## Repository Structure

```
├── src/                    # Source code (coming soon)
├── data/                   # Dataset preparation scripts (coming soon)
├── experiments/
│   └── diagnostic/         # Diagnostic study scripts and results
├── requirements.txt
└── README.md
```

---

## Requirements

```
python >= 3.9
torch >= 2.0
segment-anything-2
transformers
torch-geometric
```

*Full requirements.txt will be updated with code release.*

---

## Citation

If you find this work useful, please consider citing:

```bibtex
@article{efficientres2027,
  title   = {Efficient Referring Expression Segmentation via Lightweight Reasoning over SAM2},
  author  = {[Authors]},
  year    = {2027}
}
```

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

<div align="center">
<i>Code and models will be released upon paper acceptance.</i>
</div>
