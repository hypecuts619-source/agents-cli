# Observability

Every `agents-cli` project ships with OpenTelemetry instrumentation that automatically exports traces to **Cloud Trace**. This gives you:

- **Distributed tracing** — track requests as they flow through LLM calls and tool executions.
- **Latency analysis** — identify performance bottlenecks by analyzing span durations.
- **Error visibility** — traces capture errors, helping pinpoint where failures occur.
- **No configuration required** — works out-of-the-box in all environments.

For ADK-based agents, **prompt-response logging** is also available. It captures model interactions (prompts, responses, tokens) and exports them to GCS, BigQuery, and Cloud Logging. Disabled locally by default, enabled automatically in deployed environments.

### Logging Behavior by Environment

| Environment | Tracing (Cloud Trace) | Prompt-Response Logging |
|---|---|---|
| **Local** (`agents-cli playground`) | Enabled | Disabled (no `LOGS_BUCKET_NAME`) |
| **Deployed** (Terraform) | Enabled | Enabled (`NO_CONTENT` mode — metadata only) |

---

## Cloud Trace

The default observability method. See [Cloud Trace](cloud-trace.md) for setup and usage.

---

## BigQuery Agent Analytics

For advanced analytics — querying patterns across conversations, token usage dashboards, and LLM-as-judge scoring on production traffic. Opt-in via the `--bq-analytics` flag during project creation.

See [BigQuery Agent Analytics](bq-agent-analytics.md) for details.
