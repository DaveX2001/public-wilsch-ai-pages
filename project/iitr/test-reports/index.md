---
publish: true
---

# DS-Kit RAG Test Reports

Raw test harness results from all experiments (2026-01-13).

---

## Reports

| Report | Description | Score |
|--------|-------------|-------|
| [Baseline](test_report_baseline.csv) | Latest production (VLM + Q&A) | 17/29 |
| [German Embeddings](test_report_german_emb.csv) | multilingual-e5-large-instruct | 16/29 |
| [GPT-OSS 20B](test_report_gpt-oss-20B.csv) | Model comparison | 18/29 |
| [Qwen3 8B](test_report_qwen3-8B.csv) | Model comparison | 19/29 |
| [Qwen3 14B](test_report_qwen3-14B.csv) | Model comparison | 18/29 |
| [V2](test_report_v2.csv) | Prompt iteration v2 | - |
| [V3](test_report_v3.csv) | Prompt iteration v3 | - |
| [V4](test_report_v4.csv) | Prompt iteration v4 | - |

---

## CSV Format

Each CSV contains:
- **Q#**: Question number (1-29)
- **Status**: PASS or FAIL
- **Question**: Test question text
- **Expected**: Ground truth expected answer
- **RAG_Response**: Actual RAG system response
- **LLM_Judgment**: Judge model's evaluation

---

## Related

- [Test Analysis](../dskit-rag-test-analysis.md) - Detailed question-by-question analysis

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

