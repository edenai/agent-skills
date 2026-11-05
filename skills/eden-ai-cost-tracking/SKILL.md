---
name: eden-ai-cost-tracking
description: Track and reduce AI API spend with Eden AI - read the cost of every request, tag calls by team, client or end user, cap per-token prices, keep prompt caches warm and use batch rates. Use when a project needs LLM cost attribution, FinOps reporting or cheaper model calls.
---

# Eden AI cost tracking and reduction

## Cost of every request

- Chat, Responses, embeddings, images, rerank, search and video responses include `cost` (USD) and `provider`.
- Text-to-speech returns raw audio, so `cost` and `provider` come back in `x-edenai-*` response headers.
- Universal AI (expert model) responses include `cost` per request.

## Attribute spend

Add `tags` (up to 10 key/value labels) to any request body, then filter and group usage by them in the Eden AI console:

```python
response = client.chat.completions.create(
    model="openai/gpt-5.5",
    messages=[{"role": "user", "content": "..."}],
    extra_body={"tags": {"team": "support", "client": "acme"}},
)
```

Tags can also be sent in the `X-EdenAI-Tags` header; header and body tags are merged. Budgets per API key (daily, weekly or monthly) and model access per role are set in the console at https://app.edenai.run

## Pay less

- Route on price: send a bare model name with `"routing": {"sort": "cost"}` (the default).
- Cap prices: `"routing": {"max_price": {"input": 1, "output": 4}}` (USD per million tokens). If no provider fits, the request fails and names the cheapest one.
- Keep the prompt cache warm: send a stable `session_id` per conversation so requests stay on the provider that holds its cache (`routing.sticky` is on by default).
- Let Eden AI choose a cheaper model: `model="@edenai"` with `"routing": {"quality_cost": 10}` (0 = best quality, 10 = cheapest that can handle the request).
- Non-urgent chat jobs: `POST /v3/batches` (JSONL, OpenAI models) runs within 24 hours at batch rates.

## Pricing model

Provider prices with no markup; a 5.5% platform fee applies only when buying credits. No subscription. Details: https://www.edenai.co/pricing
