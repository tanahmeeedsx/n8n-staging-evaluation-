# n8n Isolated Staging Evaluation

This repository documents the evaluation of an isolated n8n staging environment for workflow functionality, persistence, and patch compatibility assessment.

## Overview

The staging environment was deployed using Docker and tested with n8n version **2.36.7**.

The evaluation focused on:

- Core workflow execution
- HTTP Request processing
- Data transformation
- Webhook processing
- Credential storage
- Container restart behavior
- Container recreation and persistence
- Static compatibility assessment of the development patch

## Environment

| Component | Configuration |
|---|---|
| Runtime | Docker |
| n8n Version | 2.36.7 |
| Deployment | Docker CLI |
| Container | `n8n-staging` |
| Persistent Volume | `n8n-staging-data` |
| Host Port | `5680` |
| Container Port | `5678` |
| Environment | Isolated local staging |
| Production Impact | None |

## Test Results

| Test | Result |
|---|---|
| n8n 2.36.7 Startup | PASS |
| Basic Workflow Execution | PASS |
| HTTP Request | PASS |
| Data Transformation | PASS |
| Webhook Processing | PASS |
| Credential Storage | PASS |
| Container Restart | PASS |
| Container Rebuild & Persistence | PASS |

## Patch Compatibility Assessment

The evaluated development patch documents compatibility with **n8n v1.119.0**, while the staging environment uses **n8n v2.36.7**.

Because of this version/build mismatch:

- Patch compatibility with n8n 2.36.7 was **not verified**
- Patch execution was **not performed**
- Enterprise-feature validation was **not completed**
- Static patch compatibility and risk assessment was completed

The primary finding is that the documented patch target version differs significantly from the staging version.

## Patch Repository

The patch source and compatibility assessment are maintained separately:

- Repository: `tanahmeeedsx/n8n-dev-license-bypass`
- Assessment Branch: `compatibility-assessment`

## Repository Structure

```text
n8n-staging-evaluation-
├── README.md
├── reports/
│   └── evaluation-report.md
├── patch-review/
│   └── static-analysis.md
├── tests/
│   └── README.md
├── workflows/
│   └── README.md
└── evidence/
    └── .gitkeep
