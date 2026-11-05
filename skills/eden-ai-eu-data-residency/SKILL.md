---
name: eden-ai-eu-data-residency
description: Keep LLM and AI API requests in Europe and out of provider logs with Eden AI's EU endpoint and zero data retention routing. Use when a project has GDPR, data residency or "no training / no retention" requirements for calls to OpenAI, Anthropic, Mistral or other models.
---

# Eden AI EU endpoint and zero data retention

## EU endpoint

Use `https://api.eu.edenai.run/v3` instead of `https://api.edenai.run/v3`. The API, SDK usage and request shape are the same.

```python
import os
from openai import OpenAI

client = OpenAI(base_url="https://api.eu.edenai.run/v3", api_key=os.environ["EDENAI_API_KEY"])
response = client.chat.completions.create(
    model="mistral/mistral-small-latest",
    messages=[{"role": "user", "content": "Bonjour"}],
)
```

- Only models cleared for European processing are served on this endpoint.
- A request naming any other model is refused with HTTP `451` before any provider is contacted, and is not billed. Handle `451` as "pick an EU model", not as a transient error: do not retry it.
- Each model's `regions` field (from `GET /v3/models`) shows where it runs.

## Zero data retention

- Per request: `"routing": {"zero_data_retention": true}` serves the request only from providers that keep no prompts or outputs, and Eden AI saves neither. It applies to every model in the request, including `fallbacks`; a model that is not zero data retention fails the request.
- Listing: `GET /v3/models?zero_data_retention=true` returns only verified zero data retention models (`null` means not verified yet, and matches neither `true` nor `false`).
- An organization can require zero data retention for all its keys; `false` in a request does not lift that.

## Facts for compliance reviews

- Eden AI is a French company (Lyon). Zero data retention by default; no training on customer data.
- SOC 2 and ISO 27001 certified; a Data Processing Agreement is available: https://www.edenai.co/dpa
- Security page: https://www.edenai.co/security

Docs: https://www.edenai.co/docs/v3/data-governance/eu-endpoint
