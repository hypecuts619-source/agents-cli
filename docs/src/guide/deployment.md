# Deployment

Deploy your agent to a development environment or production with a CI/CD pipeline.

![Prototype to Production](../assets/prototype_to_prod.png)

---

## Infrastructure vs Deployment

**Infrastructure** (`agents-cli infra`) provisions the cloud resources your agent needs — service accounts, IAM bindings, APIs, telemetry buckets, and Terraform state. It sets the stage but doesn't run your agent.

**Deployment** (`agents-cli deploy`) takes your agent code and puts it on the provisioned infrastructure — building a container, pushing it to a registry, and starting the service.

The typical flow: provision infrastructure first, then deploy on top of it.

---

## Deploy to a Dev Environment

The simplest path to a running deployment:

**1. Set your dev project:**

```bash
gcloud config set project YOUR_DEV_PROJECT_ID
```

**2. Deploy the agent:**

```bash
agents-cli deploy
```

The command reads your `deployment_target` from `pyproject.toml` and dispatches to the right flow:

| `deployment_target`  | What happens                                  |
|----------------------|-----------------------------------------------|
| `agent_runtime`      | Agent Runtime deployment (fully managed)       |
| `cloud_run`          | `gcloud beta run deploy` (container on Cloud Run) |
| `gke`                | Terraform + Docker build + `kubectl apply`     |

The deployment target is set when you create your project:

```bash
agents-cli create my-agent -d cloud_run    # or agent_runtime, gke
```

To change the deployment target for an existing project, use `scaffold enhance`:

```bash
agents-cli scaffold enhance -d cloud_run
```

Run `agents-cli scaffold enhance --help` to see all available options.

!!! tip
    To enable observability features (prompt-response logging, content logs), run `agents-cli infra single-project` after deploying. Terraform provisions the telemetry resources and updates your service to use them. See the [Observability Guide](observability/index.md) for details.

**Verify it works:**

```bash
agents-cli deploy --list    # List deployments
agents-cli deploy --status  # Check deployment status
```

---

## Deployment Targets

### Agent Runtime

*Selected with `agents-cli create my-agent -d agent_runtime` or `deployment_target = "agent_runtime"` in `pyproject.toml`.*

Fully managed — no containers or infrastructure to manage:

```bash
agents-cli deploy --project my-gcp-project --region us-east1
```

Check on an async deployment:

```bash
agents-cli deploy --no-wait     # Start and return immediately
agents-cli deploy --status      # Check progress later
```

### Cloud Run

*Selected with `agents-cli create my-agent -d cloud_run` or `deployment_target = "cloud_run"` in `pyproject.toml`.*

Builds a container from source and deploys as a Cloud Run service:

```bash
agents-cli deploy --project my-gcp-project --region us-east1
```

Override resource limits:

```bash
agents-cli deploy --memory 8Gi --port 8080
```

Deploy a pre-built image instead of building from source:

```bash
agents-cli deploy --image gcr.io/my-project/my-agent:v1
```

!!! tip
    If you need more advanced Cloud Run deployment features not exposed via `agents-cli` flags, use `--dry-run` (or `-n`) to print the full `gcloud` command. You can then copy it and add additional arguments as needed.

### GKE

*Selected with `agents-cli create my-agent -d gke` or `deployment_target = "gke"` in `pyproject.toml`.*

Deploys to a GKE cluster using Terraform and kubectl:

```bash
agents-cli deploy --cluster-name my-cluster --project my-gcp-project
```

---

## Next Steps

- [CI/CD & Production](cicd.md) — set up automated pipelines for staging and production
- [Observability](observability/index.md) — monitor your deployed agent
