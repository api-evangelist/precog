---
name: Generate a semantic model and add context with Precog
description: Kick off a semantic-model generation job over a pipeline, review detected cross-source relationships, and attach semantic contexts so AI assistants can query the data with meaning.
api: openapi/precog-public-http-api-openapi.yaml
operations:
- post-semantic-model-generation-jobs
- get-semantic-model-generation-jobs
- get-semantic-model-generation-jobs-job-id
- post-semantic-model-generation-jobs-job-id-cancel
- get-data-model-relationship-candidates
- get-pipelines-pipeline-id-semantic-contexts
- post-pipelines-pipeline-id-semantic-contexts
---

# Generate a semantic model and add context

Turn ingested Precog data into a queryable semantic layer. All requests require
`Authorization: Bearer <token>`; base URL `https://api.precog.cloud`.

## Steps

1. **Start a generation job** — `post-semantic-model-generation-jobs`
   (`POST /semantic-model/generation-jobs`). Returns a job id (async, `202`).
2. **Poll for completion** — `get-semantic-model-generation-jobs-job-id`
   (`GET /semantic-model/generation-jobs/{job-id}`); list all with
   `get-semantic-model-generation-jobs` (`GET /semantic-model/generation-jobs`).
   Cancel a running job with `post-semantic-model-generation-jobs-job-id-cancel`
   (`POST /semantic-model/generation-jobs/{job-id}/cancel`).
3. **Review relationship candidates** — `get-data-model-relationship-candidates`
   (`GET /data-model/relationship-candidates`) to inspect detected cross-source
   joins before trusting them.
4. **Inspect existing semantic contexts** —
   `get-pipelines-pipeline-id-semantic-contexts`
   (`GET /pipelines/{pipeline-id}/semantic-contexts`).
5. **Attach a semantic context** —
   `post-pipelines-pipeline-id-semantic-contexts`
   (`POST /pipelines/{pipeline-id}/semantic-contexts`) to inject business logic,
   definitions and relationships used when the AI assistant answers questions.

## Notes
- The generated semantic model follows the JSON Schema in
  `json-schema/precog-foundation-semantic-model.schema.json`.
- Precog's MCP server (`mcp/precog-mcp.yml`) is the agent-native way to query the
  resulting semantic layer from Claude/ChatGPT.
