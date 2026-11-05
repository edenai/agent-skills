---
name: eden-ai-llm-gateway
description: Call any LLM (OpenAI, Anthropic, Google, Mistral and 80+ providers) through Eden AI's OpenAI-compatible API, with provider routing by cost, speed or latency and automatic fallbacks. Use when code needs one API key for many models, a drop-in OpenAI base URL, or cheaper and more reliable LLM calls.
---

# Eden AI LLM gateway

Eden AI is an OpenAI-compatible AI gateway: one API key and one request shape for around 1,000 models from 80+ providers. Existing OpenAI SDK code works by changing the base URL.

## Setup

- Base URL: `https://api.edenai.run/v3` (EU-only processing: `https://api.eu.edenai.run/v3`, see the `eden-ai-eu-data-residency` skill).
- Auth: `Authorization: Bearer <EDEN_AI_API_KEY>`. Get a key at https://app.edenai.run/user/register
- Never hard-code the key; read it from an environment variable such as `EDENAI_API_KEY`.

```python
import os
from openai import OpenAI

client = OpenAI(base_url="https://api.edenai.run/v3", api_key=os.environ["EDENAI_API_KEY"])

response = client.chat.completions.create(
    model="openai/gpt-5.5",
    messages=[{"role": "user", "content": "Hello"}],
)
print(response.choices[0].message.content)
```

## Choosing the model

- `provider/model` (for example `openai/gpt-5.5`) calls that exact provider.
- A bare model name (for example `gpt-5.5`) lets Eden AI pick among the providers that sell it, using `routing`.
- `@edenai` lets Eden AI pick the model itself; restrict it with `router_candidates` and trade quality for cost with `routing.quality_cost` (0 = best, 10 = cheapest).
- List models: `GET /v3/models` (one entry per provider/model) or `GET /v3/models?view=models` (one entry per routable name with its provider endpoints, prices, context length and regions). No key needed.

## Routing and fallbacks

Send Eden AI fields in the request body (with the OpenAI SDK, through `extra_body`):

```python
response = client.chat.completions.create(
    model="gpt-5.5",  # no provider prefix: Eden AI picks the provider
    messages=[{"role": "user", "content": "Summarize this ticket: ..."}],
    extra_body={
        "routing": {"sort": "cost", "allow_fallbacks": True},
        "fallbacks": ["mistral/mistral-small-latest"],
    },
)
```

- `routing.sort`: `cost` (default), `speed`, `latency` or `exact` (most reliable for tool calls and structured output). Also accepted as a model suffix, e.g. `gpt-5.5:speed`.
- `routing.allowed_providers` / `routing.ignored_providers`: restrict or exclude providers.
- `routing.order`: providers to try first, in order.
- `routing.max_price`: ceiling in USD per million tokens, e.g. `{"input": 2.5, "output": 10}`.
- `routing.allow_fallbacks`: set `false` to pin the request to one provider.
- `fallbacks`: up to 7 other models to try, in order, if the primary model fails.

## Other compatible endpoints

- Anthropic Messages format: `POST /v3/v1/messages` (Anthropic SDK with base URL `https://api.edenai.run/v3`).
- OpenAI Responses API: `POST /v3/responses`.
- Embeddings: `POST /v3/embeddings`. Moderation: `POST /v3/moderations`.

## Reading the response

Responses keep the OpenAI shape and add `provider` (who served the request) and `cost` (USD). A `422` response carries a `detail` list describing invalid fields.

## MCP

Eden AI expert models are also available as MCP tools at `https://mcp.edenai.run/mcp` (streamable HTTP, `Authorization: Bearer <EDEN_AI_API_KEY>`).

Docs: https://www.edenai.co/docs · OpenAPI: https://api.edenai.run/v3/docs/openapi.json
