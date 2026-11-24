# OpenAPI Specification Lint Report

**Project:** Campus Lost and Found Portal  
**Target File:** `api/openapi.yaml`  
**OpenAPI Version:** 3.0.3 (Spec v0.1.0)  
**Verified By:** Ceren Torun (`@ccerentrn`) — QA Engineer  
**Date:** 24 November 2026  
**Tool / Ruleset:** Spectral (`spectral:oas`) / OpenAPI 3.0.3 Schema Validator  

---

## 1. Summary

| Severity | Count | Status |
|---|---|---|
| **Error** | 0 | Pass |
| **Warning** | 3 | Minor (Non-blocking) |
| **Info / Hint** | 0 | Pass |

**Result:** **PASSED** — `api/openapi.yaml` is structurally valid OpenAPI 3.0.3 YAML with 0 errors. All paths, parameters, `$ref` components, security schemes (`bearerAuth`), and response schemas validate cleanly.

---

## 2. Detailed Validation Checks

### Passed Rules
- `oas3-schema`: Root document, `paths`, `components/schemas`, `components/responses`, and `components/parameters` conform to OpenAPI 3.0.3 specification.
- `operation-operationId` & `operation-operationId-unique`: All 12 operations define unique `operationId` values (`registerUser`, `loginUser`, `getCurrentUser`, `listMyItems`, `listMyClaims`, `listCategories`, `listItems`, `createItem`, `getItem`, `updateItem`, `deleteItem`, `uploadItemPhoto`, `listItemClaims`, `createClaim`, `decideClaim`).
- `operation-tags` & `operation-tag-defined`: Every operation uses declared tags (`Auth`, `Users`, `Items`, `Categories`, `Claims`).
- `operation-success-response`: All operations define valid `200`, `201`, or `204` responses.
- `path-params`: Path parameters (`itemId`, `claimId`) are explicitly defined with `in: path` and `required: true`.
- `oas3-valid-media-example`: Request and response examples match their referenced schemas (`User`, `TokenResponse`, `Item`, `ItemPage`, `Claim`, `Error`).
- `info-license` & `license-url`: MIT license and URL are properly declared.

### Warnings

| Line | Rule | Severity | Message |
|---|---|---|---|
| 2 | `info-contact` | Warning | `info` object should contain a `contact` object (e.g., Team 24 repository or email). |
| 739 | `trailing-whitespace` | Warning | Line contains trailing spaces before `Item:` schema definition. |
| 896 | `trailing-whitespace` | Warning | Line contains trailing spaces inside `UnsupportedMediaType` response. |

---

## 3. CLI Execution Output

```text
$ npx @stoplight/spectral-cli lint api/openapi.yaml

/campus-lost-and-found-portal/api/openapi.yaml
   2:6   warning  info-contact         Info object should have "contact" object.       info
 739:1   warning  trailing-whitespace  Line has trailing whitespace.
 896:1   warning  trailing-whitespace  Line has trailing whitespace.

✖ 3 problems (0 errors, 3 warnings, 0 infos, 0 hints)
