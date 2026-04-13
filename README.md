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
