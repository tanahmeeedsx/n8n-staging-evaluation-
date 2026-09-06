# 🧪 n8n Isolated Staging Evaluation

> **DevOps / Infrastructure Testing Project**
> Isolated Docker-based staging environment for evaluating n8n workflow functionality, persistence, operational stability, and compatibility of a development license patch.

---

## 📌 Project Overview

This repository documents an isolated staging evaluation of **n8n** using Docker.

The primary goal was to establish a clean and reproducible n8n staging environment, validate normal workflow functionality, test persistence across container lifecycle operations, and perform a **static compatibility assessment** of the `n8n-dev-license-bypass` patch.

The patch itself was **not executed** against the staging instance because the patch documentation targets **n8n v1.119.0**, while the staging environment uses **n8n v2.36.7**. Applying a source-level patch across this version gap without compatibility validation could introduce build failures or unexpected behavior.

> **Important:** This evaluation focuses on safe staging validation and compatibility assessment. No production systems or production credentials were used.

---

## 🎯 Evaluation Objectives

The staging evaluation focused on the following areas:

* 🐳 Deploy a clean n8n instance using Docker
* ⚙️ Validate basic workflow execution
* 🌐 Validate webhook processing
* 🔗 Validate HTTP Request functionality
* 🔄 Validate data transformation
* 🔐 Validate credential storage
* ♻️ Validate container restart behavior
* 💾 Validate persistence after container recreation
* 🔍 Review patch source and documented dependencies
* ⚠️ Identify version compatibility risks
* 📝 Document limitations and operational considerations

---

## 🏗️ Staging Architecture

```text
┌─────────────────────────────────────────────┐
│              Ubuntu Host System              │
│                                             │
│  ┌───────────────────────────────────────┐  │
│  │          Docker Container             │  │
│  │                                       │  │
│  │          n8n v2.36.7                  │  │
│  │          Port: 5678                   │  │
│  │                                       │  │
│  │  • Workflow Execution                 │  │
│  │  • Webhook Processing                 │  │
│  │  • HTTP Requests                      │  │
│  │  • Data Transformation                │  │
│  │  • Credential Storage                 │  │
│  └──────────────────┬────────────────────┘  │
│                     │                       │
│                     ▼                       │
│          Docker Persistent Volume           │
│             n8n-staging-data                │
│                                             │
└─────────────────────────────────────────────┘
```

### Isolation Model

The evaluation environment was isolated from existing services:

* Dedicated Docker container
* Dedicated Docker volume
* Dedicated host port
* Non-production test credentials
* No production workflows
* No production data
* No production services modified

---

## 🧰 Environment

| Component         | Configuration               |
| ----------------- | --------------------------- |
| Host OS           | Ubuntu Linux                |
| Container Runtime | Docker                      |
| Application       | n8n                         |
| n8n Version       | `2.36.7`                    |
| Container Name    | `n8n-staging`               |
| Host Port         | `5680`                      |
| Container Port    | `5678`                      |
| Persistent Volume | `n8n-staging-data`          |
| Deployment Type   | Standalone Docker Container |
| Database          | Default local n8n storage   |
| Environment       | Isolated staging            |

---

## 🚀 Deployment Validation

The staging instance was deployed using a dedicated Docker volume:

```bash
docker volume create n8n-staging-data
```

The n8n container was started with:

```bash
docker run -d \
  --name n8n-staging \
  -p 5680:5678 \
  -v n8n-staging-data:/home/node/.n8n \
  n8nio/n8n:2.36.7
```

The n8n editor was successfully accessed through:

```text
http://localhost:5680
```

### Deployment Result

**PASS ✅**

The container started successfully and the n8n editor became available.

---

# 🧪 Functional Test Matrix

| Test                          | Result        | Status     |
| ----------------------------- | ------------- | ---------- |
| n8n Container Startup         | Successful    | ✅ PASS     |
| Basic Workflow Execution      | Successful    | ✅ PASS     |
| HTTP Request                  | Successful    | ✅ PASS     |
| Data Transformation           | Successful    | ✅ PASS     |
| Webhook Processing            | Successful    | ✅ PASS     |
| Credential Storage            | Successful    | ✅ PASS     |
| Container Restart             | Successful    | ✅ PASS     |
| Container Recreation          | Successful    | ✅ PASS     |
| Persistent Data Recovery      | Successful    | ✅ PASS     |
| Patch Execution               | Not performed | ⚠️ BLOCKED |
| Enterprise Feature Validation | Not performed | ⚠️ BLOCKED |

---

# 🔬 Detailed Test Results

## 1. Basic Workflow Execution

### Workflow

```text
Manual Trigger
      │
      ▼
Edit Fields
      │
      ▼
Execution Success
```

Test value:

```text
status = staging-test
```

### Result

Workflow executed successfully.

**Status: ✅ PASS**

---

## 2. Webhook Processing

A webhook workflow was configured using:

```text
POST /webhook-test/baseline-test
```

Test request:

```bash
curl -X POST http://localhost:5680/webhook-test/baseline-test \
  -H "Content-Type: application/json" \
  -d '{"test":"baseline","source":"docker-staging"}'
```

The webhook returned a successful workflow-start response and the execution completed successfully.

**Status: ✅ PASS**

---

## 3. Credential Storage

A non-production Header Auth credential was created for testing:

```text
Name: X-Staging-Test
Value: staging-only-test
```

The credential was successfully stored in n8n.

**Status: ✅ PASS**

> No production credentials were used.

---

## 4. Data Transformation

Test workflow:

```text
Manual Trigger
      │
      ▼
Edit Fields
      │
      ▼
Transformed Test Data
```

Test fields:

```text
name = n8n-staging
environment = test
status = active
```

Execution completed successfully.

**Status: ✅ PASS**

---

## 5. HTTP Request

The HTTP Request node was tested against:

```text
https://httpbin.org/get
```

Method:

```text
GET
```

The request completed successfully and returned the expected HTTP response.

**Status: ✅ PASS**

---

# ♻️ Lifecycle & Persistence Testing

## Container Restart

The running n8n container was restarted:

```bash
docker restart n8n-staging
```

After restart:

* Workflows remained available
* Credentials remained available
* n8n editor remained accessible
* Workflow execution continued normally

**Status: ✅ PASS**

---

## Container Recreation

The container was completely removed and recreated:

```bash
docker stop n8n-staging
docker rm n8n-staging
```

Then recreated using the same persistent Docker volume:

```bash
docker run -d \
  --name n8n-staging \
  -p 5680:5678 \
  -v n8n-staging-data:/home/node/.n8n \
  n8nio/n8n:2.36.7
```

After recreation, the following remained available:

* `Baseline Workflow Test`
* `Data Transformation Test`
* `X-Staging-Test` credential

This confirms that the dedicated Docker volume successfully preserved the n8n application data across container replacement.

**Status: ✅ PASS**

---

# 🔍 Patch Compatibility Assessment

## Patch Repository

The development license patch was reviewed from the forked repository:

```text
n8n-dev-license-bypass
```

The assessment branch:

```text
compatibility-assessment
```

The patch documentation identifies **n8n v1.119.0** as its compatibility target.

The staging environment, however, uses:

```text
n8n v2.36.7
```

### Compatibility Difference

```text
Patch Target
    │
    ▼
n8n v1.119.0
    │
    │  Version gap
    ▼
n8n v2.36.7
    │
    ▼
Staging Environment
```

Because the patch modifies n8n source files, this version difference is operationally significant.

---

# 🧩 Static Patch Review

The patch was inspected without executing it against the running staging environment.

The reviewed areas included:

```text
packages/@n8n/backend-common/src/logging/logger.ts

packages/@n8n/node-cli/src/configs/eslint.ts

packages/cli/src/license.ts

packages/frontend/editor-ui/src/app/components/EnterpriseEdition.ee.vue

packages/frontend/editor-ui/src/app/stores/settings.store.ts

packages/frontend/editor-ui/src/shims.d.ts

pnpm-workspace.yaml
```

### Static Assessment

The patch affects multiple areas of the n8n source tree, including:

* Backend/license-related logic
* Frontend enterprise UI behavior
* Application settings
* Build/workspace configuration
* Development environment behavior

Because multiple source-level components are modified, compatibility cannot safely be assumed across major/minor n8n version changes.

---

# ⚠️ Patch Execution Status

The patch was **not executed** against n8n v2.36.7.

### Reason

The documented patch target is:

```text
n8n v1.119.0
```

while the staging environment is:

```text
n8n v2.36.7
```

Executing the patch without first establishing source compatibility could result in:

* Build failures
* Missing or changed source paths
* TypeScript compilation errors
* Frontend compilation failures
* Runtime regressions
* License subsystem instability
* Unexpected behavior during upgrades

Therefore, patch execution was treated as **BLOCKED pending version compatibility validation**.

**Status: ⚠️ BLOCKED**

---

# 🚫 Enterprise Feature Validation

Enterprise/advanced feature activation was not validated through the patch.

Reason:

```text
Patch execution
      │
      ▼
Version mismatch detected
      │
      ▼
Patch execution blocked
      │
      ▼
Enterprise validation not performed
```

This means the evaluation does **not** claim that the patch successfully activates enterprise features on n8n v2.36.7.

**Status: ⚠️ NOT VALIDATED**

---

# 📊 Overall Findings

### Successfully Validated

```text
Docker Deployment             ✅
n8n Startup                   ✅
Basic Workflow Execution      ✅
HTTP Request                  ✅
Data Transformation           ✅
Webhook Processing            ✅
Credential Storage            ✅
Container Restart             ✅
Container Recreation          ✅
Persistent Data               ✅
```

### Not Validated

```text
Patch Execution               ⚠️ BLOCKED
Enterprise Feature Activation ⚠️ NOT VALIDATED
Before/After Patch Comparison ⚠️ NOT PERFORMED
```

---

# ⚠️ Risks & Limitations

## Version Compatibility Risk

The largest identified risk is the difference between the documented patch target and the staging n8n version.

```text
Documented Patch Target: n8n 1.119.0
Staging Version:         n8n 2.36.7
```

Source-level compatibility should be established before attempting any patch execution.

## Upgrade Risk

A source-modifying patch may require maintenance after n8n upgrades.

Potential upgrade impacts include:

* Changed source files
* Renamed functions
* Changed APIs
* Changed frontend components
* Dependency updates
* Build system changes

## Validation Limitation

Because the patch was not executed:

* Enterprise feature activation could not be confirmed
* Patch runtime behavior could not be measured
* Patch-related regressions could not be directly tested
* Before/after comparison could not be performed

These limitations are explicitly recorded rather than inferred.

---

# 🛡️ Safety & Isolation

The evaluation was performed as a staging-only exercise.

### Controls

* Dedicated Docker container
* Dedicated persistent volume
* Non-production test data
* Non-production credential
* No production workflow modification
* No production service modification
* No production credential usage

The environment can be removed independently without affecting unrelated services.

---

# 📁 Repository Structure

```text
n8n-staging-evaluation-
├── README.md
├── EVALUATION-REPORT.md
│
├── evidence/
│   └── .gitkeep
│
├── patch-review/
│   └── static-analysis.md
│
├── tests/
│   └── README.md
│
└── workflows/
    └── README.md
```

### Directory Purpose

| Path                   | Purpose                                 |
| ---------------------- | --------------------------------------- |
| `README.md`            | Project overview and evaluation summary |
| `EVALUATION-REPORT.md` | Detailed evaluation report              |
| `evidence/`            | Test evidence and supporting artifacts  |
| `patch-review/`        | Patch compatibility and static analysis |
| `tests/`               | Test documentation                      |
| `workflows/`           | Workflow-related documentation          |

---

# 📚 Documentation

The repository contains documentation covering:

* Staging environment setup
* Functional testing
* Persistence testing
* Container lifecycle testing
* Patch compatibility assessment
* Static source review
* Risks and limitations
* Final evaluation findings

---

# ✅ Final Evaluation

The isolated n8n staging environment was successfully deployed and validated.

All planned **non-patch functional and persistence tests passed**, including workflow execution, webhook processing, HTTP requests, data transformation, credential storage, container restart, and complete container recreation with persistent data recovery.

The development license patch was **not executed** because its documented compatibility target (`n8n v1.119.0`) does not match the staging version (`n8n v2.36.7`).

Therefore:

> **The staging environment is operationally healthy, but the patch itself remains unvalidated due to a documented version compatibility gap.**

This provides a clear technical basis for deciding whether a version-aligned patch test should be performed separately.

---

## 🧾 Evaluation Summary

| Area                             | Result           |
| -------------------------------- | ---------------- |
| Staging Deployment               | ✅ PASS           |
| Workflow Execution               | ✅ PASS           |
| Webhook Processing               | ✅ PASS           |
| HTTP Request                     | ✅ PASS           |
| Data Transformation              | ✅ PASS           |
| Credential Storage               | ✅ PASS           |
| Restart Persistence              | ✅ PASS           |
| Container Recreation Persistence | ✅ PASS           |
| Patch Static Review              | ✅ COMPLETED      |
| Patch Execution                  | ⚠️ BLOCKED       |
| Enterprise Validation            | ⚠️ NOT VALIDATED |
| Production Impact                | ✅ NONE           |

---

**Evaluation Type:** Isolated Staging Test
**Application:** n8n
**Staging Version:** `2.36.7`
**Patch Target Version:** `1.119.0`
**Environment:** Docker / Ubuntu
**Final Status:** 🟡 **Staging Validated — Patch Compatibility Blocked**
