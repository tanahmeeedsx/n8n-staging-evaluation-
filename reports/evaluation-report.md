# n8n Isolated Staging Evaluation Report

## 1. Executive Summary

This report documents the evaluation of an isolated n8n staging environment created to assess core workflow functionality, persistence behavior, and compatibility considerations for the development patch under evaluation.

The staging environment was deployed using Docker with **n8n v2.36.7**. Functional and persistence tests were completed successfully.

The evaluated development patch documents compatibility with **n8n v1.119.0**. Because the staging environment uses n8n v2.36.7, patch compatibility with the tested version was not established and patch execution was not performed.

The primary outcome of this evaluation is that the clean n8n v2.36.7 staging environment remained stable across the completed baseline, workflow, webhook, credential, restart, and container recreation tests.

---

## 2. Evaluation Objectives

The evaluation focused on the following objectives:

* Deploy an isolated n8n staging environment.
* Verify n8n startup and basic operation.
* Test standard workflow execution.
* Test HTTP Request processing.
* Test data transformation.
* Test webhook processing.
* Validate credential storage.
* Validate service restart behavior.
* Validate persistence after container recreation.
* Perform static compatibility assessment of the development patch.
* Identify version, build, stability, and maintenance risks.

---

## 3. Test Environment

| Component                    | Configuration          |
| ---------------------------- | ---------------------- |
| Host Environment             | Ubuntu Linux           |
| Container Runtime            | Docker                 |
| n8n Version                  | 2.36.7                 |
| Container Name               | `n8n-staging`          |
| Docker Volume                | `n8n-staging-data`     |
| Host Port                    | `5680`                 |
| Container Port               | `5678`                 |
| Deployment Method            | Docker CLI             |
| Environment Type             | Isolated local staging |
| Production Services Affected | None                   |

The n8n instance was configured with a persistent Docker volume mounted at:

```text
/home/node/.n8n
```

The environment was used only for testing and contained no production credentials or production data.

---

## 4. Deployment Validation

The n8n staging container was successfully deployed using the official n8n Docker image:

```text
n8nio/n8n:2.36.7
```

The running container was verified using Docker and the n8n interface.

The environment successfully started and provided access to the n8n editor.

**Result: PASS**

---

## 5. Functional Test Results

### 5.1 Basic Workflow Execution

A baseline workflow was created using a Manual Trigger and Edit Fields node.

Test data included:

```text
status = staging-test
```

The workflow executed successfully.

**Result: PASS**

---

### 5.2 HTTP Request

A dedicated HTTP Request workflow was created.

Configuration:

```text
Method: GET
Endpoint: https://httpbin.org/get
```

The request completed successfully and returned a valid response.

**Result: PASS**

---

### 5.3 Data Transformation

A dedicated data transformation workflow was created using Manual Trigger and Edit Fields.

Test fields:

```text
name = n8n-staging
environment = test
status = active
```

The workflow executed successfully and produced the expected transformed data.

**Result: PASS**

---

### 5.4 Webhook Processing

A webhook workflow was tested using the n8n webhook test endpoint.

Test request:

```bash
curl -X POST http://localhost:5680/webhook-test/baseline-test \
  -H "Content-Type: application/json" \
  -d '{"test":"baseline","source":"docker-staging"}'
```

The webhook request was accepted and the workflow execution completed successfully.

**Result: PASS**

---

### 5.5 Credential Storage

A test Header Auth credential named:

```text
X-Staging-Test
```

was created using non-production test data.

The credential was saved successfully and remained available after the staging instance was restarted and recreated.

**Result: PASS**

---

## 6. Persistence and Recovery Testing

### 6.1 Container Restart

The n8n container was restarted using Docker:

```bash
docker restart n8n-staging
```

After restart:

* Existing workflows remained available.
* Stored test credentials remained available.
* n8n returned to normal operation.

**Result: PASS**

---

### 6.2 Container Recreation

The original container was stopped and removed:

```bash
docker stop n8n-staging
docker rm n8n-staging
```

The container was then recreated using the same Docker image and persistent volume.

After recreation, the following were verified:

* Existing workflows remained available.
* Test credentials remained available.
* n8n was operational.
* Persistent staging data was retained.

**Result: PASS**

---

## 7. Consolidated Test Results

| Test                            | Result |
| ------------------------------- | ------ |
| n8n 2.36.7 Startup              | PASS   |
| Basic Workflow Execution        | PASS   |
| HTTP Request                    | PASS   |
| Data Transformation             | PASS   |
| Webhook Processing              | PASS   |
| Credential Storage              | PASS   |
| Container Restart               | PASS   |
| Container Rebuild & Persistence | PASS   |

**Overall baseline functional result: PASS**

---

## 8. Patch Compatibility Assessment

The development patch under evaluation documents compatibility with:

```text
n8n v1.119.0
```

The staging environment was running:

```text
n8n v2.36.7
```

Therefore, the documented patch target and the staging version do not match.

### Assessment Outcome

| Assessment                    | Result        |
| ----------------------------- | ------------- |
| Static Patch Review           | Completed     |
| Documented Patch Target       | n8n v1.119.0  |
| Staging Version               | n8n v2.36.7   |
| Compatibility with v2.36.7    | Not Verified  |
| Patch Execution               | Not Performed |
| Enterprise Feature Validation | Not Completed |
| Before/After Comparison       | Not Performed |

The patch was not applied to the n8n 2.36.7 staging instance because the documented compatibility target differs from the tested version and build-level compatibility could not be established safely.

---

## 9. Static Patch Review

The patch was reviewed to identify affected areas, version assumptions, and potential compatibility risks.

The reviewed patch modifies multiple n8n components, including backend licensing-related logic, frontend enterprise-related components, configuration files, and workspace configuration.

The patch documentation identifies **n8n v1.119.0** as its compatibility target.

This creates a significant compatibility risk when evaluating the patch against n8n v2.36.7.

### Identified Risks

* Version mismatch between the documented patch target and staging version.
* Potential patch conflicts caused by source-code changes between n8n versions.
* Build dependency differences.
* Potential maintenance overhead when upgrading n8n.
* Possible incompatibility with newer frontend/backend source structures.
* Enterprise-feature validation cannot be concluded without a compatible and legitimately authorized test setup.

---

## 10. Stability Findings

The clean n8n v2.36.7 staging environment remained operational throughout the completed tests.

The following areas were successfully validated:

* Workflow execution
* HTTP Request processing
* Data transformation
* Webhook processing
* Credential storage
* Service restart
* Container recreation
* Persistent data retention

No production services or production credentials were involved in the testing environment.

---

## 11. Limitations

This evaluation has the following limitations:

1. The development patch was not executed against n8n v2.36.7.
2. Patch compatibility with n8n v2.36.7 was therefore not established.
3. Enterprise-feature activation/validation was not completed.
4. No before-and-after comparison between patched and unpatched environments was performed.
5. Results represent the tested Docker staging environment and should not be interpreted as production validation.

---

## 12. Conclusion

The isolated n8n v2.36.7 staging environment successfully passed all completed functional and persistence tests.

Basic workflows, HTTP requests, data transformation, webhooks, credential storage, container restart, and container recreation were successfully validated.

The main limitation identified during patch assessment is the version/build mismatch between the documented patch target (**n8n v1.119.0**) and the tested staging version (**n8n v2.36.7**).

As a result, the patch was not deployed and enterprise-feature validation was not completed.

The evaluation therefore establishes a successful baseline for the clean n8n 2.36.7 environment while documenting the patch compatibility limitation and associated operational risks.

**Final Assessment:**

> Clean n8n 2.36.7 staging environment: **PASS**
> Patch compatibility with n8n 2.36.7: **NOT VERIFIED**
> Patch deployment: **NOT PERFORMED**
