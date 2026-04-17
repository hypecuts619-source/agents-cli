# Evaluation Guide

Run structured evaluations to confirm your agent calls the right tools, produces quality responses, and handles edge cases. Under the hood, `agents-cli eval run` uses the [ADK eval CLI](https://google.github.io/adk-docs/evaluate/) to run evaluations with LLM-as-judge scoring.

---

## Run Your First Evaluation

Your project includes a default eval set at `tests/eval/evalsets/basic.evalset.json` and scoring criteria at `tests/eval/eval_config.json`. Run it:

```bash
agents-cli eval run
```

The output shows scores for each eval case against the configured rubrics. A score above the threshold (default: 0.8) passes.

```bash
# Run a specific eval set
agents-cli eval run --evalset tests/eval/evalsets/custom.evalset.json

# Run all eval sets in the project
agents-cli eval run --all
```

---

## Writing Eval Cases and Choosing Metrics

For full documentation on eval case schemas, available metrics, rubric writing, and multi-turn testing, see the [ADK Evaluation Guide](https://google.github.io/adk-docs/evaluate/).

Quick reference for choosing metrics:

- **Agents with custom function tools** — use `tool_trajectory_avg_score` + `rubric_based_final_response_quality_v1`.
- **Agents with `google_search` or model-internal tools** — use only `rubric_based_final_response_quality_v1` (model-internal tools don't appear in trajectory).
- **RAG agents** — use `rubric_based_final_response_quality_v1` + `hallucinations_v1`.

---

## The Eval-Fix Loop

Evaluation is iterative. Expect 5-10+ cycles before your agent consistently passes.

1. **Write 1-2 core eval cases** covering the most important behavior.
2. **Run**: `agents-cli eval run`
3. **Read the results** — which cases failed and why.
4. **Fix** — adjust the agent's instruction, tools, or logic.
5. **Re-run**: `agents-cli eval run`
6. **Expand** — once core cases pass, add edge cases and new scenarios.

For full documentation on eval case schemas, metrics, rubrics, and user simulation, see the [ADK Evaluation Guide](https://google.github.io/adk-docs/evaluate/).
