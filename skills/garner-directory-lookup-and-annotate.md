---
name: Directory lookup and accuracy annotation
description: Look up a professional or facility by id from the Garner directory and submit an accuracy annotation to correct a provider record.
api: openapi/garner-openapi-original.json
operations: [GetProfessional, GetFacility, CreateProviderAnnotations]
---

# Directory lookup and accuracy annotation

Use the Garner directory endpoints to hydrate provider records and to report
inaccuracies back to Garner.

## Auth
- Obtain a Bearer token via `POST https://api.getgarner.com/oauth2/token`
  (`grant_type=client_credentials`) and send `Authorization: Bearer <token>`
  plus `Accept-Version: 1`. See `authentication/garner-authentication.yml`.

## Steps
1. **GetProfessional** — `GET /professionals/{professional_id}` for clinician
   detail, specialties, networks, and locations.
2. **GetFacility** — `GET /facilities/{facility_id}` for facility detail,
   specialty data, and directory locations.
3. **CreateProviderAnnotations** — `POST /provider-annotations/{provider_id}`
   with the `CreateAnnotationParams` body to flag/correct a provider-record field
   for accuracy.

## Rules
- `404` means the id is unknown; `422` means the request failed validation
  (check the annotation field names against `AnnotationField`).
- Errors return the `ServiceError` envelope (`requestId`, `message`, `data`);
  log `requestId` when contacting support. See `errors/garner-problem-types.yml`.
- Annotations are corrections, not create-provider calls — only submit fields you
  can substantiate.
