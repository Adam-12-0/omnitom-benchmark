# OmniToM: Benchmarking Theory of Mind in LLMs via Explicit Belief Modeling

<p align="center">
  <a href="https://arxiv.org/abs/2605.26322"><img src="https://img.shields.io/badge/arXiv-2605.26322-b31b1b.svg" alt="arXiv"></a>
  <a href="https://adam-12-0.github.io/omnitom-project/"><img src="https://img.shields.io/badge/Project-Page-blue.svg" alt="Project Page"></a>
  <a href="https://github.com/Adam-12-0/omnitom-benchmark"><img src="https://img.shields.io/badge/GitHub-Benchmark-black.svg?logo=github" alt="GitHub"></a>
  <a href="https://huggingface.co/datasets/Adam-12-0/omnitom-benchmark"><img src="https://img.shields.io/badge/Hugging%20Face-Dataset-yellow.svg?logo=huggingface" alt="Hugging Face Dataset"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License"></a>
</p>

<p align="center">
  <b>Adam Bawatneh · Sagar Sapkota · Amrit Singh Bedi · Santu Karmaker · Mubarak Shah</b>
</p>

---

Social reasoning requires tracking how information is distributed across actors, not only what happened in the world. Existing Theory of Mind (ToM) benchmarks evaluate this ability through endpoint question answering: a model is scored by whether it produces the correct final answer, leaving the underlying mental-state representation entirely unobserved. A model can answer *"Where will Bob look?"* correctly while failing to track the belief states that make the answer valid.

**OmniToM** closes this gap through *explicit belief-structure modeling*. Rather than probing endpoints, OmniToM requires a model to reconstruct the full multi-actor belief representation of a story, then label each belief proposition along seven dimensions grounded in ATOMS, a literature-derived taxonomy of Theory of Mind abilities. This makes the hidden reasoning process visible and directly measurable.

<img src="assets/figure2_omnitom_pipeline.png" alt="OmniToM two-stage evaluation pipeline" width="100%" />

## Benchmark at a Glance

| | |
|---|---|
| **Stories** | 895 |
| **Labeled belief propositions** | 22,343 |
| **Total schema labels** | 156,401 (22,343 × 7) |
| **Story categories** | 7 (from ToMBench) |
| **Human annotation effort** | 1,000+ person-hours |
| **Evaluation stages** | 2 (Extraction + Labeling) |

### Category Breakdown

| Category | Stories | Beliefs | Avg. beliefs/story |
| --- | ---: | ---: | ---: |
| Ambiguous Story Task | 98 | 4,614 | 47.08 |
| False Belief Task | 97 | 2,168 | 22.35 |
| Faux-pas Recognition Test | 142 | 3,981 | 28.04 |
| Hinting Task Test | 100 | 2,317 | 23.17 |
| Persuasion Story Task | 97 | 1,204 | 12.41 |
| Scalar Implicature Test | 154 | 3,251 | 21.11 |
| Strange Story Task | 207 | 4,808 | 23.23 |
| **Total** | **895** | **22,343** | **24.96** |

### Belief-Order Distribution

| Order | Share |
| --- | ---: |
| Order 0 (world facts) | 32.57% |
| Order 1 (actor beliefs) | 57.12% |
| Order 2+ (nested beliefs) | 10.32% |

## Evaluation Protocol

OmniToM evaluates two linked tasks under zero-shot TELeR Level 3 prompts:

**Stage 1, Belief Extraction:** Given a story, extract all relevant `(Actor, Belief, Order)` tuples. Scored by a GPT-5 semantic judge using precision, recall, and F1 over matched propositions.

**Stage 2, Belief Labeling:** Given a story and the benchmark belief table, assign a seven-dimensional schema label vector to each belief. Scored by exact-match accuracy per dimension, averaged over all propositions.

The seven schema dimensions are: **Order · Truth Status · Knowledge Access · Representation · Content Type · Mental Source · Context**

<img src="assets/figure3_annotation_pipeline.png" alt="OmniToM annotation pipeline" width="100%" />

<img src="assets/figure4_schema_label_distribution.png" alt="OmniToM label distribution" width="100%" />

## Results

### Main Benchmark Results (Zero-Shot, TELeR L3)

The camera-ready main results report story-macro Stage 1 F1 with 95% bootstrap confidence intervals, endpoint QA accuracy, and story-macro Stage 2 accuracy. GPT-5 is omitted from Stage 1 because it serves as the semantic judge. **Bold** = best, <u>underline</u> = second-best.

| Model | Stage 1 F1 [95% CI] | Endpoint QA |
| --- | ---: | ---: |
| **Closed-source** | | |
| Gemini-2.5 Pro | **61.63 [60.88, 62.39]** | **82.77** |
| Gemini-2.5 Flash | 55.75 [54.63, 56.82] | <u>81.82</u> |
| GPT-5 | N/A | N/A |
| **Open-weight** | | |
| Gemma-3 27B | <u>58.03 [57.05, 59.01]</u> | 58.47 |
| Mistral-Small 24B | 56.46 [55.53, 57.41] | 76.55 |
| Mistral-Large 123B | 53.53 [52.61, 54.44] | 77.39 |
| Qwen3 32B | 51.63 [50.68, 52.56] | 73.62 |
| Llama-3.3 70B | 47.12 [46.12, 48.09] | 76.22 |
| Qwen3 8B | 42.70 [41.50, 43.85] | 64.84 |
| Llama-3.1 8B | 37.21 [36.02, 38.38] | 63.95 |

**Stage 2, Belief-Labeling Accuracy (%)**

| Model | Order | Status | Access | Repr | CType | Source | Context | **Overall** |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| **Closed-source** | | | | | | | | |
| Gemini-2.5 Pro | 96.23 | <u>86.50</u> | 66.94 | **89.01** | **86.35** | **86.70** | 87.07 | <u>85.54</u> |
| Gemini-2.5 Flash | 95.56 | 84.97 | 71.34 | <u>87.58</u> | <u>85.97</u> | 84.10 | <u>92.14</u> | **85.95** |
| GPT-5 | 95.18 | 82.72 | 66.85 | 83.42 | 79.96 | 83.02 | 88.83 | 82.85 |
| **Open-weight** | | | | | | | | |
| Gemma-3 27B | <u>96.56</u> | 82.44 | 71.57 | 54.33 | 73.50 | 78.72 | 92.07 | 78.46 |
| Mistral-Small 24B | 95.13 | 82.22 | **74.59** | 62.79 | 76.01 | 84.82 | 91.90 | 81.06 |
| Mistral-Large 123B | **97.25** | **86.53** | <u>74.14</u> | 72.87 | 82.83 | <u>86.32</u> | **92.97** | 84.70 |
| Qwen3 32B | 96.42 | 82.43 | 73.91 | 62.45 | 71.27 | 76.84 | 90.81 | 79.16 |
| Llama-3.3 70B | 92.74 | 83.55 | 67.41 | 72.43 | 72.35 | 76.71 | 91.69 | 79.55 |
| Qwen3 8B | 73.38 | 67.17 | 57.94 | 63.77 | 51.43 | 61.49 | 74.62 | 64.26 |
| Llama-3.1 8B | 71.90 | 65.59 | 56.13 | 64.40 | 48.63 | 55.18 | 76.81 | 62.66 |

### Detailed Results

The table below retains the category-wise Stage 1 breakdown.

#### Stage 1, Belief Extraction F1 (%)

| Model | Params | AST | FBT | FPT | HT | PST | SIT | SST | **Overall** |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| **Closed-source** | | | | | | | | | |
| Gemini-2.5 Pro | N/A | **54.33** | <u>71.55</u> | **62.11** | **55.10** | **64.84** | 63.74 | **60.21** | **61.63** |
| Gemini-2.5 Flash | N/A | 42.40 | 56.48 | 57.78 | <u>50.34</u> | <u>58.55</u> | 62.91 | <u>56.31</u> | 55.75 |
| GPT-5 | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| **Open-weight** | | | | | | | | | |
| Gemma-3 27B | 27B | 48.72 | **72.39** | 56.05 | 45.46 | 56.72 | **68.76** | 55.77 | <u>58.03</u> |
| Mistral-Small 24B | 24B | <u>52.97</u> | 54.58 | <u>59.79</u> | 48.32 | 56.97 | <u>66.20</u> | 53.17 | 56.46 |
| Mistral-Large 123B | 123B | 47.75 | 71.28 | 53.66 | 41.78 | 58.53 | 57.38 | 48.35 | 53.53 |
| Qwen3 32B | 32B | 46.88 | 57.32 | 53.38 | 41.41 | 57.25 | 56.51 | 48.67 | 51.63 |
| Llama-3.3 70B | 70B | 37.51 | 64.07 | 46.33 | 36.27 | 47.23 | 57.70 | 41.58 | 47.12 |
| Qwen3 8B | 8B | 38.72 | 49.70 | 43.59 | 37.14 | 47.87 | 47.20 | 37.60 | 42.70 |
| Llama-3.1 8B | 8B | 26.34 | 48.29 | 35.80 | 30.85 | 35.01 | 53.52 | 30.08 | 37.21 |

### Key Finding: Actor-Specific Information Tracking is the Core Bottleneck

OmniToM localizes a consistent failure mode across both stages. **Stage 1** extraction F1 shows a consistent gap between Order 0 world facts and actor-specific beliefs. Moving beyond world facts requires the model to determine which facts each actor perceived, missed, remembered, was told, or could infer. **Stage 2** results point to difficulty with *Knowledge Access* (56.13-74.59%), with several models also struggling on *Representation* (54.33-89.01%). Order 1 beliefs have the lowest overall labeling accuracy (72.2%), including 59.2% for Knowledge Access and 60.1% for Representation. Order 2+ accuracy is higher overall (75.3%), so the results do not show a monotonic decline with belief order.

The gap between the two stages is also diagnostic: models label provided belief propositions far better (up to 85.95%) than they extract those propositions from raw text (up to 61.63%). This shows that current LLMs are much better at operating over an explicit belief structure once it is given than at constructing that structure directly from story text, a distinction invisible to endpoint QA benchmarks.

### Additional Experiments

**Endpoint QA.** The main table reports accuracy on the linked ToMBench questions. Gemini-2.5 Pro reaches 82.77% and Gemini-2.5 Flash reaches 81.82%. The endpoint rankings differ from the Stage 1 extraction rankings, showing that endpoint answers and explicit belief reconstruction measure different model behavior.

**Reference and judge sensitivity.** Replacing the Claude reference with an independently generated Mistral-Large reference raises Stage 1 scores for all seven eligible models while preserving the broad ordering, with two adjacent swaps. Replacing GPT-5 with a Llama-3.3 judge shifts scores upward by 15.93-21.90 points and swaps Mistral-Small and Mistral-Large. GPT-5 is retained because its human agreement is higher (87.76% versus 75.85%).

**Class imbalance.** Context labels are 91.81% Neutral, and a majority-label baseline reaches 91.54% accuracy. The camera-ready appendix therefore reports macro-F1 and balanced accuracy alongside raw Stage 2 accuracy.

## Dataset Format

Each line of `benchmark_story_belief_labels.jsonl` is one JSON object:

```json
{
  "story_id": 1,
  "story_category": "Ambiguous Story Task",
  "story": "Story text...",
  "beliefs": [
    {
      "actor": "world",
      "belief": "A minimal propositional statement.",
      "labels": {
        "order": "0",
        "truth_status": "True",
        "knowledge_access": "Public",
        "representation": "Explicit",
        "content_type": "Action/Event",
        "mental_source": "Narration",
        "context": "Neutral"
      }
    }
  ]
}
```

## Included Files

| File | Description |
| --- | --- |
| `benchmark_story_belief_labels.jsonl` | Benchmark dataset, one JSON object per story |
| `benchmark_prompting.py` | Helpers for loading stories and reconstructing benchmark tables |
| `extraction_prompts.py` | Stage 1 extraction prompt builder |
| `label_prompts.py` | Stage 2 labeling prompt builder |
| `judge_prompts.py` | Semantic-judge prompt builder with three judge examples |
| `run_replication.py` | End-to-end experiment and evaluation runner |
| `HF_DATASET_CARD.md` | Dataset card for dataset-hosting platforms |
| `LICENSE` | License for the OmniToM release package |

## Quick Prompt Usage

```python
from benchmark_prompting import load_story_record
from prompts_extract import build_extract_messages
from prompts_label import build_label_messages
from prompt_evaluate import build_evaluation_messages

record = load_story_record(1)
extract_system, extract_user = build_extract_messages(1)
label_system, label_user = build_label_messages(1)

predictions_csv = 'Actor,Belief\nworld,Example belief\n'
judge_system, judge_user = build_evaluation_messages(1, predictions_csv, fewshots=3)
```

## Installation

For the mock smoke test, Python `3.10+` and the standard library are sufficient. For model runs:

```bash
pip install -r requirements.txt
```

Configure credentials via environment variables:

```bash
export OPENAI_API_KEY="..."
export GOOGLE_API_KEY="..."
# For OpenAI-compatible endpoints:
export OPENAI_BASE_URL="https://your-endpoint.example/v1"
```

## Running the Experiments

**Smoke test** (no API required):

```bash
python run_replication.py --backend mock --output-dir runs/mock_smoke
```

**Open-weight model** (Stage 1 + Stage 2):

```bash
python run_replication.py \
  --backend hf \
  --model meta-llama/Llama-3.3-70B-Instruct \
  --load-in-4bit --bnb-4bit-compute-dtype bf16 \
  --stages extract label \
  --output-dir runs/llama33_70b
```

**API model** (Stage 1 + Stage 2):

```bash
python run_replication.py \
  --backend api \
  --api-provider google_genai \
  --model gemini-2.5-flash \
  --stages extract label \
  --output-dir runs/gemini25_flash
```

**GPT-5 Stage 2 labeling only** (GPT-5 is the judge, so is excluded from Stage 1):

```bash
python run_replication.py \
  --backend api --api-provider openai \
  --model gpt-5 --stages label \
  --output-dir runs/gpt5_stage2
```

**Semantic judge + metrics** (after Stage 1 extraction exists):

```bash
python run_replication.py \
  --backend api --api-provider openai \
  --judge-model gpt-5 --judge-fewshots 3 \
  --stages judge metrics \
  --output-dir runs/llama33_70b
```

Useful flags: `--story-ids 1,2,5-7` · `--max-stories 25` · `--overwrite` · `--no-story-context` · `--max-new-tokens 6000` · `--temperature 0`

## Output Layout

```
<output-dir>/
  manifest.json
  extraction/raw/  extraction/csv/
  labeling/raw/    labeling/csv/
  judge/raw/       judge/csv/
  metrics/
    stage1_story_metrics.csv   stage1_overall.csv   stage1_by_category.csv
    stage2_story_metrics.csv   stage2_overall.csv   stage2_by_category.csv
```

## Important Notice

OmniToM is released for evaluation and analysis. To limit benchmark contamination, please use the released stories for benchmarking rather than training.

## Citation

```bibtex
@article{bawatneh2026omnitom,
  title     = {OmniToM: Benchmarking Theory of Mind in LLMs via Explicit Belief Modeling},
  author    = {Bawatneh, Adam and Sapkota, Sagar and Bedi, Amrit Singh and Karmaker, Santu and Shah, Mubarak},
  journal   = {arXiv preprint arXiv:2605.26322},
  year      = {2026}
}
```

## License and Attribution

The OmniToM release package is distributed under the license in `LICENSE`.

Story text is derived from ToMBench (MIT License, Copyright 2024 Zhuang Chen):

- Repository: [`zhchen18/ToMBench`](https://github.com/zhchen18/ToMBench)
- Paper: "ToMBench: Benchmarking Theory of Mind in Large Language Models" (ACL 2024)

OmniToM adds new benchmark annotations, prompt builders, replication code, and release documentation on top of the ToMBench story corpus.
