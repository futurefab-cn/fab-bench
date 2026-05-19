# FAB-Bench: A Framework for Adaptive RAG Benchmarking in Semiconductor Manufacturing

<p align="center">
  <a href="#benchmark-dataset">Benchmark</a> |
  <a href="#evaluation-metrics">Metrics</a> |
  <a href="#paper">Paper</a>
</p>

**FAB-Bench** (*Framework for Adaptive Benchmarking*) is an end-to-end framework for automated, quantitative evaluation of Retrieval-Augmented Generation (RAG) systems in semiconductor manufacturing. Unlike expert-dependent evaluations, FAB-Bench provides interpretable, reproducible, and domain-grounded scores, enabling scalable benchmarking on proprietary, highly technical corpora.

## Highlights

- **Six-Dimensional Evaluation**: Measures Factuality, Context Utilization, Completeness, Relevance, Technical Depth, and Support Quality -- enabling precise failure attribution across retrieval and generation.
- **Adaptive Benchmark Generation**: Automatically generates domain-specific QA pairs from user-provided corpora with adaptive temperature control and semantic deduplication.
- **Cross-Document Synthesis**: Three synthesis strategies (needle-in-haystack, intra-document multi-topic, cross-document multi-hop) test genuine RAG reasoning beyond parametric knowledge.
- **Context Window Scaling Analysis (4K--32K)**: Identifies three distinct scaling behaviors -- logarithmic growth, early saturation, and cold-start dynamics -- providing actionable deployment guidance.

## System Overview

<p align="center">
  <img src="Fab-Bench High-Level.drawio.png" width="80%" alt="FAB-Bench System Overview">
</p>

FAB-Bench consists of two core components:

1. **Adaptive Benchmark Generation System**: Parses domain documents into structured representations, synthesizes cross-document contexts, and generates QA pairs with adaptive temperature control based on technical content density.

2. **Automated Multi-Dimensional Evaluation Platform**: Evaluates RAG system responses across six orthogonal metrics via G-Eval, supporting both cross-model and cross-framework comparison.

## Benchmark Dataset

Our benchmark is constructed from a semiconductor manufacturing corpus comprising:
- **150+** peer-reviewed papers (IEDM, ISSCC, VLSI)
- **70+** patent documents with proprietary fabrication details
- **SEMI** industry standard specifications
- **~347 million tokens** across **188 topics**

From **1,300+** generated candidates, we curated **200 high-quality QA pairs** covering three test categories:

| Category | # Questions | Objective | Subjective | Primary Metric |
|---|---|---|---|---|
| **ROB** (Retrieval Robustness) | 59 | 33 | 26 | Factuality |
| **MULTI** (Multi-hop Reasoning) | 90 | 49 | 41 | Completeness |
| **GEN** (Generation Quality) | 51 | 36 | 15 | Technical Depth |
| **Total** | **200** | **118 (59%)** | **82 (41%)** | -- |

### Download

The curated benchmark is available at [`QAs/`](QAs/):

```
QAs/
├── 1_retrieval_robustness/     # 59 ROB test cases (ROB_001.json -- ROB_059.json)
├── 2_retrieval_multihop/       # 90 MULTI test cases (MULTI_001.json -- MULTI_090.json)
├── 3_generation_quality/       # 51 GEN test cases (GEN_001.json -- GEN_051.json)
└── _benchmark_summary.json     # Index with all test IDs, types, and questions
```

### Data Format

Each QA entry is a self-contained JSON file:

```json
{
  "test_id": "ROB_001",
  "test_type": "robustness",
  "question_format": "OBJECTIVE",
  "question": "[Judge] In the context of the chemical mechanical polishing (CMP) model...",
  "gold_context_sources": [
    {
      "section_title": "A model for chemical-mechanical polishing...",
      "evidence": "Figs. 5a and b illustrate the effect of...",
      "doc_id": "1-s2.0-S0022509603001285-main",
      "has_image": false
    }
  ],
  "ground_truth_answer": "The correct answer is False. Reasoning: ...",
  "primary_metric": "factuality",
  "secondary_metrics": ["depth", "completeness", "relevance", "context_utilization", "support_quality"],
  "generation_metadata": {
    "temperature_used": 0.74,
    "context_diversity_score": 1.0,
    "context_type": "balanced_cross_dataset",
    "semiconductor_analysis": {
      "technical_density": 0.00105,
      "precision_level": "high",
      "dominant_category": "manufacturing"
    }
  }
}
```

**Field descriptions:**

| Field | Description |
|---|---|
| `test_id` | Unique identifier (e.g., `ROB_001`, `MULTI_042`, `GEN_017`) |
| `test_type` | One of `robustness`, `multi_hop`, `generation_quality` |
| `question_format` | `OBJECTIVE` (with prefix `[Choice]`, `[Judge]`, `[Fill]`, `[Calculate]`) or `SUBJECTIVE` |
| `question` | The benchmark question |
| `gold_context_sources` | Ground-truth evidence passages with section titles and document IDs |
| `ground_truth_answer` | Reference answer with reasoning chain |
| `primary_metric` | The main evaluation dimension for this test type |
| `generation_metadata` | Provenance: temperature, diversity score, technical density analysis |

### Benchmark Statistics

| Property | Value |
|---|---|
| Total QA pairs | 200 (curated from 1,300+) |
| Corpus size | ~347M tokens |
| Source documents | 220+ (papers + patents + standards) |
| Topic coverage | 188 topics |
| Domain terms indexed | 431 (7 semantic categories) |
| Question formats | Objective (59%) + Subjective (41%) |

## Evaluation Metrics

| Metric | What It Evaluates |
|---|---|
| **Completeness** | Coverage of all required technical elements |
| **Technical Depth** | Sophistication of analysis and reasoning |
| **Factuality** | Alignment with sources; absence of hallucination |
| **Relevance** | Focus on query; no off-topic content |
| **Context Utilization** | Use of retrieved vs. parametric knowledge |
| **Support Quality** | Specificity and accuracy of citations |

These metrics are orthogonal by design -- a response may exhibit high relevance but low factuality (hallucinating on-topic content), or strong depth but poor context utilization (ignoring retrieved documents). This enables diagnosing distinct failure modes: retrieval failures manifest as low context utilization despite high factuality, while generation failures appear as uniformly low scores.

## Paper

> **FAB-Bench: A Framework for Adaptive RAG Benchmarking in Semiconductor Manufacturing**
>
> Jingbin Qian, Congwen Yi, Min Xia, Wen Wu, Jun Zhu, Jian Guan\*
>
> FutureFab.AI · \*Corresponding author: andrewg@futurefab.ai

- [Paper PDF](FAB_Bench__A_Framework_for_Adaptive_RAG_Benchmarking_in_Semiconductor_Manufacturing.pdf)

