---
name: Find top providers near a member
description: Use the Garner Health API to return ranked Top Provider recommendations near a location for a given specialty and plan, then pull full detail for a chosen professional.
api: openapi/garner-openapi-original.json
operations: [GetRankedProviders, GetProfessional]
---

# Find top providers near a member

Garner ranks providers using claims-based quality/cost analytics. This skill
returns ranked recommendations and then hydrates the chosen clinician.

## Auth
1. Exchange your `client_id` + `client_secret` at `POST https://api.getgarner.com/oauth2/token`
   with `grant_type=client_credentials` (form-urlencoded) for a Bearer `access_token`
   (lifetime ~900s). See `authentication/garner-authentication.yml`.
2. Send `Authorization: Bearer <access_token>` and the required `Accept-Version: 1`
   header on every request.

## Steps
1. **GetRankedProviders** — `GET /providers` in ranked mode. Provide the member
   position (`lat`/`lng` or `zipCode`), the `specialty`, the `plan`, optional
   `networkId`, and `limit`. Returns a ranked `RankedProviderList`.
2. Select the top `RankedProvider` and read its professional id.
3. **GetProfessional** — `GET /professionals/{professional_id}` to pull
   per-specialty quality metrics and practice locations for display.

## Rules
- Results are ordered by Garner's recommendation score (distance, quality, cost);
  do not re-sort.
- Handle `422` (validation — check required position/specialty/plan) and, on
  detail lookups, `404` (unknown id). Errors use the `ServiceError` envelope
  (`requestId`, `message`, `data`) — not RFC 9457. See `errors/garner-problem-types.yml`.
- No idempotency keys; these are read operations. Cap result size with `limit`.
