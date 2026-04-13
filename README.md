# 🌐 Top API Testing Interview Questions (With Real-World Examples)
> A practical, hands-on guide for QA Engineers & SDETs preparing for API testing interviews. Covers validation strategies, testing types, auth flows, CI/CD integration, and error handling.

## 📖 Table of Contents
- [1. Validating API Responses Beyond Status Codes](#1-how-do-you-validate-an-api-response-beyond-the-status-code)
- [2. Contract Testing vs Integration Testing](#2-difference-between-contract-testing-and-integration-testing)
- [3. Testing Authentication & Authorization](#3-how-do-you-test-authentication-and-authorization-flows)
- [4. API Testing in CI/CD Pipelines](#4-how-do-you-handle-api-testing-in-cicd)
- [5. Testing API Error Handling](#5-what-is-your-approach-to-testing-api-error-handling)
- [💡 Bonus: Follow-Up Questions to Prepare](#bonus-smart-follow-up-questions)

---

## 1. How do you validate an API response beyond the status code?
A `200 OK` or `404 Not Found` is just the starting point. Real validation covers data, structure, behavior, and performance.

**Example:** `GET /api/products/123`
```json
{
  "id": 123,
  "name": "iPhone 13",
  "price": 150000,
  "inStock": true
}
```
### ✅ What to Validate
| Validation Layer | What to Check | Why It Matters |
|------------------|---------------|----------------|
| **Data Correctness** | `id` matches request, `price` is numeric, `inStock` is boolean | Prevents type coercion bugs & ensures accurate data mapping |
| **Schema Validation** | Required fields exist, types match contract (e.g., JSON Schema) | Catches silent breaking changes before they hit consumers |
| **Business Logic** | `price ≥ 0`, `inStock=false` triggers correct downstream/UI behavior | Validates real-world rules, not just structural compliance |
| **Headers** | `Content-Type: application/json`, cache & security headers present | Ensures proper parsing, caching strategy, and security posture |
| **Response Time** | Typically `< 2s` (adjust per SLA/SLO) | Catches performance regressions early in the pipeline |

### 💡 Interview Answer
> *"I don’t stop at the status code. I validate the response body for data correctness and schema compliance, verify business rules are enforced, check headers for security and content type, and measure response time against SLAs. This ensures the API is not just reachable, but reliable and consumer-ready."*

🛠️ **Pro Tip:** Automate schema & type validation using tools like `ajv`, `zod`, Postman's `pm.response.to.have.jsonSchema()`, or Cypress `cy.request().its('body').should('match', schema)`.

---

## 2. Difference between contract testing and integration testing
Both are critical for API reliability, but they solve completely different problems. Interviewers often test your ability to distinguish them.

### 📊 Comparison at a Glance
| Aspect | Contract Testing | Integration Testing |
|--------|------------------|---------------------|
| **Goal** | Validate API structure & data agreements between producer & consumer | Verify end-to-end workflows across multiple services/systems |
| **Focus** | Request/response schema, field names, data types, required vs optional | System interaction, data flow, state changes, third-party dependencies |
| **Example Failure** | Backend changes `price` from `number` → `string` (`"150000"`) → ❌ Contract breaks | Order API → Payment Service → Inventory DB → Payment succeeds but stock isn’t updated → ❌ Workflow breaks |
| **When to Run** | Early in CI, per service, fast & isolated | After contract tests, in staging/test environments, slightly slower |
| **Common Tools** | Pact, OpenAPI/Swagger validators, Spring Cloud Contract, Specmatic | Cypress, Postman/Newman, RestAssured, Testcontainers, k6 |

### 💡 Interview Answer
> *"Contract testing validates that the producer and consumer agree on the API structure—fields, types, and formats. Integration testing verifies that multiple services or systems actually work together end-to-end, handling real data flows and state changes. One catches breaking changes early; the other catches workflow failures in complex architectures."*

🛠️ **Pro Tip:** Run contract tests as a fast CI gate before integration tests. This prevents wasted debugging time when integration failures are just caused by silent schema mismatches.

---
## 3. How do you test authentication and authorization flows
Security testing isn't optional. You must verify both *who* the user is (AuthN) and *what* they're allowed to do (AuthZ).

### 🧪 Test Strategy: Login → Access Protected Resource
**Step 1: Authentication (AuthN) – `POST /login`**
| Scenario | Expected Response |
|----------|-------------------|
| Valid credentials | `200 OK` + JWT/Access Token + (optional) Refresh Token |
| Invalid credentials | `401 Unauthorized` + generic error (no user enumeration) |

**Step 2: Authorization (AuthZ) – `GET /orders`**
| Scenario | Expected Response |
|----------|-------------------|
| Valid token attached | `200 OK` + user-specific data |
| Missing/Expired token | `401 Unauthorized` |
| Valid token, wrong role (e.g., user → admin endpoint) | `403 Forbidden` |

### 🔍 Additional Security & Edge-Case Checks
- ⏳ **Token Lifecycle:** Expiry validation, refresh token rotation, logout invalidation
- 🛡️ **Token Tampering:** Modify JWT payload or signature → expect `401/403`
- 🔄 **State Changes:** Token reuse after password reset, concurrent session limits
- 📜 **Headers & Security:** `Authorization: Bearer <token>`, proper `WWW-Authenticate` on failure, secure cookie flags (if applicable)

### 💡 Interview Answer
> *"I test authentication by validating login flows, token issuance, and proper error handling for invalid credentials. For authorization, I verify role-based access control, test missing/expired/tampered tokens, and ensure endpoints return 401 for unauthenticated requests and 403 for insufficient permissions—covering the full token lifecycle and security edge cases."*

🛠️ **Pro Tip:** Never hardcode tokens in test suites. Use setup hooks to dynamically fetch, inject, and refresh tokens via your framework (e.g., `cy.request()` in Cypress, `pre-request scripts` in Postman, or `@pytest.fixture` in Python). This keeps tests resilient across environments.
