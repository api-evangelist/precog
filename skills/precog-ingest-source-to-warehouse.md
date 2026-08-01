---
name: Ingest a source into a data warehouse with Precog
description: Connect a business application as a source, connect a destination warehouse, create a pipeline between them, and run the first load.
api: openapi/precog-public-http-api-openapi.yaml
operations:
- get-kinds-source
- post-sources
- auth-source-by-id
- get-kinds-destination
- post-destinations
- post-pipelines
- post-pipelines-pipeline-id-loads
- get-pipelines-pipeline-id-status
---

# Ingest a source into a warehouse

Use the Precog public HTTP API to stand up an end-to-end ingest pipeline. All
requests require an HTTP `Authorization: Bearer <token>` header. The production
base URL is `https://api.precog.cloud` (the spec ships a `localhost:30000` dev
server placeholder). Errors come back as `{ "error": { "type", "details" } }`
(see `errors/precog-problem-types.yml`); a `402 Payment Required` means the trial
expired with no active subscription.

## Steps

1. **Discover source connector types** — `get-kinds-source` (`GET /kinds/source`)
   to list the available source kinds, then `get-kinds-kind-id`
   (`GET /kinds/{kind-id}`) for a kind's configuration schema.
2. **Create the source** — `post-sources` (`POST /sources`) with the chosen kind
   and its connector config.
3. **Authenticate the source if required** — if the source needs auth (OAuth 2.0
   or Google service-account), call `auth-source-by-id`
   (`PUT /sources/{source-id}/auth`).
4. **Create the destination warehouse** — discover kinds with
   `get-kinds-destination` (`GET /kinds/destination`), then `post-destinations`
   (`POST /destinations`).
5. **Create the pipeline** — `post-pipelines` (`POST /pipelines`) linking the
   `source-id` and `destination-id`.
6. **Run the first load** — `post-pipelines-pipeline-id-loads`
   (`POST /pipelines/{pipeline-id}/loads`).
7. **Monitor progress** — poll `get-pipelines-pipeline-id-status`
   (`GET /pipelines/{pipeline-id}/status`) and
   `get-pipelines-pipeline-id-metrics` until the load completes.

## Notes
- No idempotency-key contract is documented, so avoid blind retries of the create
  operations (see `conventions/precog-conventions.yml`).
- Collection endpoints return full arrays (no pagination).
