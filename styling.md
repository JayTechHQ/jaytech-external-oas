# API Contract Ruleset & Naming Conventions

This repository defines both **structural rules** (enforced by Spectral) and **styling/naming conventions** (for humans and AI agents) to ensure all OpenAPI contracts are consistent.

---

## 1. Spectral Ruleset Instructions

These are validated automatically by the Spectral ruleset.

### General
- Must validate against **OpenAPI 3.0.3**.
- `operationId` must be **unique** per operation.
- Every operation must define:
  - `summary`
  - `description`
  - `tags`
  - Success and error responses.

### Paths
- No trailing `/` in paths.
- Path params must use `{camelCaseId}` format.
- Path parameters must be declared in `components/parameters`.

### Requests & Responses
- Request and response bodies must use `$ref` schemas (no inline definitions).
- Error responses must include `x-correlation-id`.
- Success responses:
  - `200` → retrieval
  - `201` → creation
  - `204` → update/delete

### Components
- No unused components.
- No sibling keys alongside `$ref`.
- Enums must be typed and have no duplicates.
- Example values must match declared type/format.

### Documentation
- `info.title`, `info.version`, and `info.description` are mandatory.
- Every tag must have a description.

### Security
- Every operation must reference a global `security` scheme.

---

## 2. Naming & Styling Instructions

These conventions are **not linted automatically** but must always be followed when designing new contracts.

### Files & Namespaces
- **Repository name**: `{org}-{domain}-oas-{service}-v{version}`  
  Example: `vng-products-oas-knowledge-service-v1`
- **Namespace**: `Org.Domain.OAS.Service.V{version}`  
  Example: `Vng.Products.OAS.KnowledgeService.V1`

### OperationIds
- Format: `{Verb}{Entity}`  
- Entity name is **singular** (plural only for collection-level endpoints).  
  Examples:  
  - `GetKeywords`  
  - `PostProvider`  
  - `PutSource`  
  - `DeleteTheme`

### Tags
- Capitalized **singular form**.  
  Example: `Provider`, `Keyword`, `Source`, `Theme`.
- Must match schema/entity naming.

### Schemas
- Namespaced with `.` for variants.  
  Examples:  
  - `Keyword`  
  - `Keyword.New`  
  - `Keyword.Status`  
  - `Source.Index.Result`
- Use **PascalCase** for schema names and properties inside `components`.
- Enums use **PascalCase values** (`Active`, `Archived`, `Suggested`).

### Parameters
- Path params: `{entityId}` in **camelCase**.  
  Example: `/providers/{providerId}`
- Sort params:
  - Parameter name: `sort`
  - Schema reference: `Sort.{Entity}`
- Pagination params:  
  - `limit`  
  - `offset`

### Responses
- Success:
  - `200` → retrieval  
  - `201` → creation  
  - `204` → update/delete  
- Always return `x-correlation-id` in headers.

### Styling
- Use block style for descriptions:  
  ```yaml
  description: |
    A long description wrapped at 80–100 characters.
