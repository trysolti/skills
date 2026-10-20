---
name: solti-quickstart
description: Automates Solti Platform onboarding from a single prompt. Installs the Solti CLI (Homebrew), logs the user in via OAuth, creates the organization and project if missing, wires up cloud connections (AWS/GCP/Azure) and default VM pools, and submits a first hello-world workload. Use when the user asks to get started with Solti, set up Solti, connect cloud resources, or submit their first Solti job.
---

# Solti Quickstart

Take the user from "no local setup" to "first workload submitted" in one
session. The user only needs to have signed up on the Solti Platform
(https://dash.solti.run) — everything else (CLI, auth, workspace, cloud
connections, first job) is bootstrapped here.

## Prerequisites

- The user has signed up on the Solti Platform (web).
- No pre-existing Solti credentials required — this skill bootstraps auth via
  OAuth. (Headless fallback: if `SOLTI_API_KEY` + `SOLTI_PROJECT_ID` are set,
  skip Steps 1–2.)
- Cloud CLIs (`aws`, `gcloud`, `az`) only if the user wants cloud connections
  or VM pools.

## Execution Flow

### Step 0: CLI Bootstrap
Check `solti --version`. If missing, install via Homebrew (the only
distribution channel — there is no npm/PyPI package) and re-check:

```bash
brew install trysolti/tap/solti
```

### Step 1: Authentication
- Run `solti account show`.
  - If it succeeds, the user is logged in — proceed.
  - If not, run `solti account login` (opens the browser for sign-in; wait for
    completion).
- If multiple accounts are logged in (`solti account list`), ask which to use.

### Step 2: Workspace Bootstrap
Determine the local machine username (e.g. `whoami`) as `<username>`.

- `solti organization list`:
  - No organization → propose the default name "`<username>`'s Organization"
    (user can override) and run `solti organization create <name>`.
  - One or more → ask which to use (default to the sole organization).
- `solti project list --org <org>`:
  - No projects → propose the default name "`<username>`'s Project" and run
    `solti project create <name> --org <org>`.
- `solti project use <project_id>` to set the active project.

**Behavioral rules**: Ask before creating anything — but never require the
user to open the dashboard. If an API call returns `429`, wait per
`Retry-After` and retry idempotent reads.

### Step 3: Cloud Connections & VM Pools

**3a. Ask what to connect.** Ask which resources the user wants to connect —
AWS accounts, GCP projects, Azure subscriptions, plus any specific storage
buckets or container registries. Do not assume or silently create cloud-side
resources.

**3b. Wire up connections.** For each resource, use the corresponding cloud
CLI (checking availability/auth first) to obtain credentials, then register
with the Solti CLI:

- **AWS**: `solti connection create --name <name> --type aws --access-key <key> --secret-key <secret>`
- **GCP**: `solti connection create --name <name> --type gcp --project-id <project> --keyfile <key.json>`

If the workload needs no cloud resources, skip to Step 4 with a note.

**3c. Default VM pools.** If the user did not provide any resources in 3a,
ask whether to create VM pools (default: **yes**). If confirmed, create the
default VM pool for each cloud provider whose CLI is available and
authenticated (AWS EC2 / GCP Compute Engine / Azure Virtual Machines). A VM
pool is created through its provider connection — one implicit pool per
connection scope (AWS account / GCP project / Azure subscription). Verify
afterwards with `solti resource list --type vm-pools`.

### Step 4: Generate Starter Job Configuration (`job.yaml`)

```yaml
name: hello-solti-quickstart
image: python:3.13-slim
command: ["python", "-c"]
args: ["print('Hello from Solti Quickstart!')"]
resources:
  cpu: 0.25
  memory: "1Gi"
tags:
  - quickstart
  - hello-world
```

### Step 5: Submit Workload

```bash
solti job run -f job.yaml
```

Poll `solti job status <name>` until a terminal state and report the result.

### Finishing
End with a short summary: account, organization, project, connections and VM
pools (if any), job id/name, and the dashboard URL
(`https://dash.solti.run/project/<project-id>`).

## Reference

- Documentation: https://docs.solti.run
- Getting Started: https://docs.solti.run/docs/getting-started/quickstart
- API reference: https://api.docs.solti.run
