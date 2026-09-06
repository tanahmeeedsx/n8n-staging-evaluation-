# 🧪 n8n Isolated Staging Evaluation

> **A controlled Docker-based staging laboratory for validating n8n workflow reliability, persistence behavior, and development-patch compatibility.**

![Docker](https://img.shields.io/badge/Docker-Staging-blue?logo=docker)
![n8n](https://img.shields.io/badge/n8n-2.36.7-orange?logo=n8n)
![Environment](https://img.shields.io/badge/Environment-Isolated%20Sandbox-success)
![Tests](https://img.shields.io/badge/Regression%20Tests-PASS-brightgreen)
![Patch](https://img.shields.io/badge/Patch-Compatibility%20Blocked-yellow)
![Status](https://img.shields.io/badge/Evaluation-Completed-success)

---

## 📌 Project Overview

This repository contains the documentation, test evidence, and compatibility assessment from an **isolated n8n staging environment** created for internal DevOps evaluation.

The primary objective was to determine whether a development patch could be evaluated safely against a controlled n8n deployment while ensuring that normal platform functionality remained stable.

Rather than testing directly against a production environment, the evaluation was performed inside a dedicated Docker container with persistent storage.

### What was evaluated?

The staging environment was used to validate:

* ⚙️ n8n installation and startup
* 🔄 Workflow execution
* 🌐 HTTP Request processing
* 🔗 Webhook processing
* 🔐 Credential storage
* 🔧 Data transformation
* ♻️ Container restart behavior
* 💾 Persistent data across container recreation
* 🔍 Static development-patch compatibility
* 🛡️ Potential compatibility and operational risks

---

# 🎯 Evaluation Objectives

The evaluation was designed around four major goals.

### 1. Establish an Isolated Environment

Deploy a clean n8n instance inside Docker without affecting any production or existing services.

### 2. Validate Core Functionality

Confirm that normal n8n functionality works correctly before performing compatibility analysis.

### 3. Assess Patch Compatibility

Compare the documented patch target version with the staging version and determine whether the patch can safely be evaluated.

### 4. Document Operational Risks

Record version conflicts, build dependencies, persistence behavior, limitations, and potential upgrade risks.

---

# 🏗️ Staging Architecture

The test environment used a lightweight Docker-based deployment.

```text
                    ┌─────────────────────────┐
                    │      Ubuntu Host        │
                    │                         │
                    │       Docker Engine     │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │     n8n-staging         │
                    │                         │
                    │       n8n v2.36.7       │
                    │                         │
                    │      Port: 5678         │
                    └────────────┬────────────┘
                                 │
                                 │ Persistent Mount
                                 ▼
                    ┌─────────────────────────┐
                    │   n8n-staging-data      │
                    │      Docker Volume      │
                    └─────────────────────────┘
```

### Isolation Model

The staging instance was intentionally separated from production workloads.

```text
Production Environment
        │
        │    NO CONNECTION
        │
        X
        │
        ▼
┌──────────────────────────┐
│   Isolated Docker Lab    │
│                          │
│   n8n 2.36.7             │
│   n8n-staging             │
│   n8n-staging-data        │
└──────────────────────────┘
```

This allowed functional and persistence testing without introducing changes to existing services.

---

# 🖥️ Environment Details

| Component         | Configuration                    |
| ----------------- | -------------------------------- |
| Host OS           | Ubuntu Linux                     |
| Runtime           | Docker                           |
| Application       | n8n                              |
| n8n Version       | `2.36.7`                         |
| Container         | `n8n-staging`                    |
| Persistent Volume | `n8n-staging-data`               |
| Host Port         | `5680`                           |
| Container Port    | `5678`                           |
| Deployment Type   | Docker CLI                       |
| Environment       | Isolated local staging           |
| Production Impact | None                             |
| Database          | Default local deployment storage |

---

# 🚀 Deployment Validation

The staging container was deployed using the official n8n container image.

The deployment was validated through:

* Container startup
* Application availability
* n8n UI accessibility
* Container logs
* Workflow execution
* Persistent storage verification

The application became accessible through:

```text
http://localhost:5680
```

The container remained isolated from production services throughout the evaluation.

---

# 🧪 Functional Test Matrix

The following regression tests were executed against the staging instance.

| Test                 | Result | Purpose                              |
| -------------------- | ------ | ------------------------------------ |
| n8n Startup          | ✅ PASS | Validate application startup         |
| Basic Workflow       | ✅ PASS | Validate workflow execution          |
| HTTP Request         | ✅ PASS | Validate external HTTP processing    |
| Data Transformation  | ✅ PASS | Validate node data handling          |
| Webhook              | ✅ PASS | Validate incoming webhook processing |
| Credential Storage   | ✅ PASS | Validate credential persistence      |
| Container Restart    | ✅ PASS | Validate restart behavior            |
| Container Recreation | ✅ PASS | Validate persistent storage          |

### Overall Result

```text
┌─────────────────────────────────────┐
│        STAGING REGRESSION TEST      │
├─────────────────────────────────────┤
│                                     │
│  Tests Executed       : 8           │
│  Passed               : 8           │
│  Failed               : 0           │
│  Blocked               : 0           │
│                                     │
│  Result               : PASS ✅     │
│                                     │
└─────────────────────────────────────┘
```

---

# ⚙️ Test Details

## 1. Basic Workflow Execution

A simple workflow was created using:

```text
Manual Trigger
      │
      ▼
Edit Fields
```

Test data:

```text
status = staging-test
```

The workflow executed successfully.

**Result:** ✅ PASS

---

## 2. HTTP Request Test

The HTTP Request node was tested using a GET request against a public test endpoint.

```text
Manual Trigger
      │
      ▼
HTTP Request
      │
      ▼
Response
```

The request completed successfully and returned valid response data.

**Result:** ✅ PASS

---

## 3. Data Transformation Test

A data transformation workflow was created with the following test fields:

```text
name        = n8n-staging
environment = test
status      = active
```

The workflow executed successfully and produced the expected structured data.

**Result:** ✅ PASS

---

## 4. Webhook Test

A webhook endpoint was configured for:

```text
POST /webhook-test/baseline-test
```

The webhook was tested with JSON input representing a staging request.

The workflow successfully received and processed the request.

**Result:** ✅ PASS

---

## 5. Credential Storage Test

A non-production test credential was created:

```text
Credential Type : Header Auth
Name            : X-Staging-Test
Value           : staging-only-test
```

The credential was saved successfully and remained available after restart and container recreation.

**Result:** ✅ PASS

---

# ♻️ Persistence & Recovery Testing

Persistence behavior was one of the key parts of the evaluation.

The n8n data directory was mapped to a Docker volume:

```text
n8n-staging-data
        │
        ▼
/home/node/.n8n
```

Two recovery scenarios were tested.

### Container Restart

```text
Running Container
       │
       ▼
docker restart
       │
       ▼
Container Running
       │
       ▼
Data Available
```

Existing workflows and credentials remained available.

**Result:** ✅ PASS

### Container Recreation

The original container was stopped and removed.

A new container was then created using the same persistent volume.

```text
Old Container
     │
     ├── STOP
     └── REMOVE
          │
          ▼
    New Container
          │
          ▼
 Same Docker Volume
          │
          ▼
 Existing Data
```

Previously created workflows and credentials remained available.

**Result:** ✅ PASS

---

# 🔍 Patch Compatibility Assessment

The development patch was reviewed separately from the staging deployment.

The patch documentation identifies:

```text
Documented Target Version
n8n v1.119.0
```

The staging environment uses:

```text
Staging Version
n8n v2.36.7
```

This created a significant version and build compatibility gap.

### Compatibility Finding

```text
Patch Target
    │
    ▼
v1.119.0
    │
    │ Version mismatch
    ▼
v2.36.7
    │
    ▼
Compatibility NOT verified
```

Because the documented patch target and staging version differ substantially, patch execution was **not performed** against n8n 2.36.7.

This decision prevented an unsupported patch from being applied to a newer build without first establishing compatibility.

---

# 🛡️ Static Patch Review

A static review of the patch source was completed to identify:

* Targeted source files
* Version assumptions
* Build dependencies
* Potential upgrade conflicts
* Maintenance concerns
* Areas likely to require changes between n8n versions

The patch modifies multiple areas of the n8n codebase, including backend, frontend, configuration, and workspace-related files.

The static review is documented separately:

📄 `patch-review/static-analysis.md`

---

# ⚠️ Important Limitations

The following limitations were identified during the evaluation.

### Version Mismatch

The patch documentation targets:

```text
n8n v1.119.0
```

while the staging environment runs:

```text
n8n v2.36.7
```

Therefore, compatibility cannot be assumed.

### Patch Execution

The patch was **not executed** against n8n 2.36.7 because the documented target version does not match the staging build.

### Enterprise Feature Validation

Enterprise-feature activation through the patch was **not validated**.

The evaluation therefore does not claim that the patch successfully enables enterprise functionality on n8n 2.36.7.

### Before/After Comparison

A direct before/after feature comparison was not performed because applying the patch to the newer staging build was outside the verified compatibility boundary.

---

# 📊 Final Evaluation

| Area                            | Status           |
| ------------------------------- | ---------------- |
| Isolated Docker Deployment      | ✅ PASS           |
| n8n 2.36.7 Startup              | ✅ PASS           |
| Workflow Execution              | ✅ PASS           |
| HTTP Request                    | ✅ PASS           |
| Data Transformation             | ✅ PASS           |
| Webhook Processing              | ✅ PASS           |
| Credential Storage              | ✅ PASS           |
| Container Restart               | ✅ PASS           |
| Container Recreation            | ✅ PASS           |
| Persistent Data                 | ✅ PASS           |
| Static Patch Review             | ✅ COMPLETED      |
| Patch Compatibility with 2.36.7 | ⚠️ NOT VERIFIED  |
| Patch Execution                 | ⏸️ NOT PERFORMED |
| Enterprise Feature Validation   | ⏸️ NOT COMPLETED |

---

# 🧠 Key Findings

### Finding 01 — Core n8n functionality is stable

All planned baseline and regression workflows executed successfully in the isolated environment.

### Finding 02 — Persistent storage works correctly

Workflows and credentials survived both container restart and complete container recreation when the persistent Docker volume was retained.

### Finding 03 — Patch compatibility is the primary blocker

The patch documentation targets an older n8n release than the version used in staging.

### Finding 04 — Static analysis is feasible without modifying the environment

The patch could be reviewed at source level to identify potential compatibility risks without executing unsupported modifications.

### Finding 05 — Production deployment should not be inferred

Successful staging regression tests do not establish that the development patch is safe for production or compatible with newer n8n releases.

---

# 📁 Repository Structure

```text
n8n-staging-evaluation-
│
├── README.md
│
├── EVALUATION-REPORT.md
│
├── patch-review/
│   └── static-analysis.md
│
├── tests/
│   └── README.md
│
├── workflows/
│   └── README.md
│
└── evidence/
    └── .gitkeep
```

### Directory Purpose

| Directory              | Purpose                                         |
| ---------------------- | ----------------------------------------------- |
| `patch-review/`        | Static compatibility and risk analysis          |
| `tests/`               | Test methodology and validation notes           |
| `workflows/`           | Workflow-specific documentation                 |
| `evidence/`            | Supporting screenshots, logs, and test evidence |
| `EVALUATION-REPORT.md` | Detailed evaluation report                      |

---

# 📚 Documentation

### Main Documentation

* 📄 `README.md` — Project overview and evaluation summary
* 📄 `EVALUATION-REPORT.md` — Detailed staging evaluation report

### Technical Documentation

* 🔍 `patch-review/static-analysis.md` — Patch compatibility and static analysis
* 🧪 `tests/README.md` — Testing methodology
* ⚙️ `workflows/README.md` — Workflow test documentation
* 📦 `evidence/` — Supporting evidence and artifacts

---

# 🔐 Safety & Isolation Notes

This evaluation was designed as a controlled staging exercise.

The environment was:

* Locally isolated
* Containerized with Docker
* Separated from production workloads
* Backed by a dedicated persistent volume
* Tested using non-production data
* Evaluated without modifying production services

The patch compatibility assessment was limited to static analysis because the documented patch target did not match the staging n8n version.

---

# 🏁 Conclusion

The isolated n8n staging environment successfully passed all planned core functionality, workflow, webhook, credential, restart, and persistence tests.

The primary limitation was **development-patch compatibility**.

The patch documentation targets **n8n v1.119.0**, while the staging environment uses **n8n v2.36.7**. Due to this version/build mismatch, patch execution and enterprise-feature validation were intentionally not performed.

From a DevOps evaluation perspective, the staging environment itself proved stable and reproducible, while the patch requires further compatibility work before it can be considered for evaluation against the newer n8n release.

> **Final Assessment:**
> 🟢 **Staging Environment — PASS**
> 🟢 **Core n8n Functionality — PASS**
> 🟢 **Persistence & Recovery — PASS**
> 🟡 **Patch Compatibility — BLOCKED BY VERSION/BUILD MISMATCH**

---

## 👨‍💻 Evaluation Context

This project was created as part of a controlled DevOps staging and compatibility assessment exercise.

The repository focuses on reproducible testing, operational safety, evidence-based evaluation, and clear documentation of both successful results and technical limitations.

---

**Status:** `Evaluation Completed`
**Environment:** `Isolated Docker Staging`
**Application:** `n8n 2.36.7`
