# JayTech Spectral Ruleset Documentation

This document describes the complete **Spectral ruleset** used to validate all OpenAPI 3.0.3 contracts.  
The ruleset is divided into **default checks** (enabled/disabled from `spectral:oas`) and **custom JayTech rules (JT###)**.

The goal is to ensure:
- Deterministic linting for AI-generated contracts.
- Consistent naming, ordering, and structure across all JayTech OAS3 specifications.
- Predictable API contract evolution.

---

## 1. Defaults (spectral:oas overrides)

The following built-in rules are explicitly disabled (`false`) to avoid noise or because they are replaced by stricter custom JT rules:

- `info-description`
- `info-contact`
- `contact-properties`
- `info-license`
- `license-url`
- `openapi-tags-alphabetical`
- `openapi-tags`
- `tag-description`
- `typed-enum`
- `duplicated-entry-in-enum`
- `no-$ref-siblings`
- `no-eval-in-markdown`
- `no-script-tags-in-markdown`
- `operation-success-response`
- `operation-operationId-unique`
- `operation-parameters`
- `operation-description`
- `operation-operationId`
- `operation-operationId-valid-in-url`
- `operation-tags`
- `operation-singular-tag`
- `operation-tag-defined`
- `path-params`
- `path-declarations-must-exist`
- `path-keys-no-trailing-slash`
- `path-not-include-query`
- `oas3-examples-value-or-externalValue`
- `oas3-parameter-description`
- `oas3-api-servers`
- `oas3-server-not-example.com`
- `oas3-server-trailing-slash`
- `oas3-operation-security-defined`

The following built-in rules remain **enabled**:
- `oas3-unused-component: true`
- `oas3-valid-media-example: true`

---

## 2. Custom JayTech Rules (JT###)

### JT001: Document Order
- **Description:** OpenAPI documents must follow this exact order:
  1. `openapi`
  2. `info`
  3. `security` (optional)
  4. `tags`
  5. `paths`
  6. `components`
- **Severity:** error

---

### JT002: Info Section
- **JT002A:** `info.title` must be present.
- **JT002B:** `info.title` must be **kebab-case** (`^[a-z0-9]+(?:-[a-z0-9]+)*$`).
- **JT002C:** `info.description` must be present.
- **JT002D:** `info.version` must be present.

---

### JT003: Tags
- **JT003A:** Each tag must have a `name`.
- **JT003B:** Each tag must have a `description`.

---

### JT004: Paths & Operations
- **JT004A:** Each operation must have a `description`.
- **JT004B:** Each operation must have at least one `tag`.
- **JT004C:** Each operation must have a `summary`.
- **JT004D:** Each operation must have an `operationId`.
- **JT004E:** Each operation must have `responses`.
- **JT004F:** Operation fields must appear in this order:
  1. `tags`
  2. `summary`
  3. `description`
  4. `operationId`
  5. `parameters` (optional)
  6. `requestBody` (optional)
  7. `responses`
  8. Custom extensions (e.g. `x-codegen-*`)
- **JT004G:** All `parameters` must be `$ref`s (no inline definitions).
- **JT004H:** All `requestBody` objects must be `$ref`s.
- **JT004I:** All `responses` must be `$ref`s.
- **JT004J:** `operationId` must start with the HTTP verb:
  - `GET → Get*`
  - `POST → Post*`
  - `PUT → Put*`
  - `PATCH → Patch*`
  - `DELETE → Delete*`
- **JT004M:** `requestBody` name must be `{operationId}Request`.
- **JT004N:** Every operation must accept `x-correlation-id` as a header parameter.
- **JT004O:** Success response naming patterns:
  - `200/201` → `{operationId}Response`
  - `201` may also use `CreatedResponse`
  - `204` must use `NoContentResponse`
- **JT004Q:** Error responses must match canonical naming:
  - `400 → BadRequestResponse`
  - `401 → UnauthorizedResponse`
  - `403 → ForbiddenResponse`
  - `404 → NotFoundResponse`
  - `409 → ConflictResponse`
  - `500 → InternalServerErrorResponse`
  - `503 → ServiceUnavailableResponse`

---

### JT005: Component Order
- **JT005A:** Components must be ordered:
  1. `securitySchemes` (optional)
  2. `parameters`
  3. `requestBodies` (optional)
  4. `responses`
  5. `headers`
  6. `schemas`

---

### JT006: Parameters
- **JT006A:** Parameters must be sorted alphabetically.
- **JT006B:** Each parameter must have a description.
- **JT006D:** Path parameters (`in: path`) must be `required: true`.
- **JT006E:** Form parameters (`style: form`) must have `explode: false`.
- **JT006F:** `x-environment` must **not** exist as a parameter.

---

### JT007: RequestBodies
- **JT007A:** Request bodies must be sorted alphabetically.
- **JT007B:** Each request body must have a description.
- **JT007D:** Each request body must specify `required`.

---

### JT008: Responses
- **JT008A:** Responses must be sorted alphabetically.
- **JT008B:** Each response must have a description.
- **JT008D:** Each response must define `x-correlation-id` header.

---

### JT009: Schemas
- **JT009A:** Schemas must be sorted alphabetically.
- **JT009B:** Each schema must have a description.
- **JT009C:** Each schema must declare `type`.
- **JT009D:** `object` schemas must define `additionalProperties`.
- **JT009E:** `array` schemas must define `uniqueItems`.
- **JT009F:** `number` properties must declare `format: float|double|decimal`.
- **JT009G:** `integer` properties must declare `format: int32|int64`.
- **JT009H:** Complex types (`object`, `array`) may only reference other complex types via `$ref`.
- **JT009I:** Complex types cannot inline enums (`enum:` must be `$ref`).
- **JT009J:** Primitive types (`string`, `integer`, `number`) cannot exist as top-level schemas — only `object|array|string` are allowed.
- **JT009L:** `object` schemas with `additionalProperties: false` must define `properties`.
- **JT009M:** `array` schemas must define `items`.
- **JT009N:** All properties must explicitly declare `nullable`.
- **JT009O:** Complex schemas (`object`, `array`) must declare `nullable`.
- **JT009P:** Enum schemas must declare `nullable`.

---

### JT010: Examples
- **JT010A:** Examples must be sorted alphabetically.
- **JT010B:** Each example must have a description.
- **JT010C:** Each example must have a `value`.

---

## 3. Summary

This ruleset enforces:

- **Strict ordering** (document sections, path fields, components, schemas).
- **Canonical naming** for request bodies, responses, and error codes.
- **Mandatory metadata** (descriptions, nullable, required).
- **Deterministic casing** (kebab-case titles, PascalCase schemas).
- **Predictable patterns** for operationIds and response naming.

The intent is that **any AI or developer following these rules will generate identical, predictable, and high-quality OpenAPI contracts**.

---

## 4. Usage

- Run linting via Spectral in CI/CD:
  ```bash
  spectral lint openapi.yaml -r .spectral.yaml
