---
name: eden-ai-expert-models
description: Run OCR, document parsing, speech-to-text, text-to-speech, translation, image analysis, web search and video generation through one Eden AI API, with provider fallbacks. Use when code needs a specialized AI model (not a chat LLM) or wants to compare or switch providers such as Google, AWS, Azure, Mistral or Deepgram.
---

# Eden AI expert models

Expert models use the same key and base URL as the LLM gateway: `https://api.edenai.run/v3`, `Authorization: Bearer <EDEN_AI_API_KEY>`.

## 1. Find the feature and its input schema

- `GET /v3/info` lists features and subfeatures (add `include=schemas` to get every schema in one call).
- `GET /v3/info/{feature}/{subfeature}` returns the `input_schema`, the `output_schema`, whether it is `sync` or `async`, and the available `models` with pricing.

## 2. Call it

Model format: `feature/subfeature/provider[/model]`. Feature inputs go inside `input`.

```bash
curl https://api.edenai.run/v3/universal-ai \
  -H "Authorization: Bearer $EDENAI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "ocr/ocr/amazon",
    "input": {"file": "https://example.com/invoice.pdf", "language": "en"},
    "fallbacks": ["google"],
    "output_format": "compact"
  }'
```

- `fallbacks`: up to 3 other providers, tried in order.
- `output_format: "compact"` trims base64 media and dense position data, which keeps responses small for LLM and agent callers.
- The response has `status` (`success` or `fail`), `output`, `provider`, `cost`, and `error` when it failed.

## Files

Pass a public URL as `file`, or upload first: `POST /v3/upload` (multipart, field `file`) returns a `file_id`, kept up to 30 days. List with `GET /v3/upload`.

## Async features

Long-running subfeatures (for example `audio/speech_to_text_async`) use `POST /v3/universal-ai/async`, which returns `202` with a `public_id`. Poll `GET /v3/universal-ai/async/{job_id}` until `status` is `success` or `fail`, or set `webhook_receiver` to be notified.

## OpenAI-compatible endpoints for common tasks

- Speech-to-text: `POST /v3/audio/transcriptions` · text-to-speech: `POST /v3/audio/speech` (voices: `GET /v3/audio/voices`)
- Images: `POST /v3/images/generations`, `POST /v3/images/edits`
- Embeddings: `POST /v3/embeddings` · rerank: `POST /v3/rerank`
- Web search: `POST /v3/search` (providers: `GET /v3/search/models`)
- Video generation: `POST /v3/videos`, then poll `GET /v3/videos/{video_id}` and download `GET /v3/videos/{video_id}/content`

Each family lists its models at `GET /v3/<family>/models`.

Docs: https://www.edenai.co/docs/v3/expert-models/listing-models
