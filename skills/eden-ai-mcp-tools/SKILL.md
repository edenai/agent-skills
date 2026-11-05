---
name: eden-ai-mcp-tools
description: Connect an agent to Eden AI's hosted MCP server to use OCR, document parsing, web search and scraping, speech, translation, image and video models as tools. Use when an MCP client (Claude Code, Codex CLI, Cline, Continue, OpenCode) or an LLM tool-calling loop needs expert AI capabilities without per-provider integrations.
---

# Eden AI MCP server

Eden AI's expert models are exposed as tools on a hosted Model Context Protocol server.

- URL: `https://mcp.edenai.run/mcp`
- Transport: streamable HTTP
- Auth: `Authorization: Bearer <EDEN_AI_API_KEY>` on the HTTP connection. Get a key at https://app.edenai.run/user/register
- Every tool call is a normal Eden AI API call: billed to that key, visible in monitoring, same rate limits and data governance rules as the REST API.

## Connect a client

Claude Code:

```bash
claude mcp add --transport http edenai https://mcp.edenai.run/mcp --header "Authorization: Bearer $EDENAI_API_KEY"
```

Ready-to-copy configs for Codex CLI, Cline, Continue, OpenCode, Hermes and OpenClaw: https://www.edenai.co/docs/v3/expert-models/mcp-server

## Tools

One tool per expert model feature (for example `ocr`, `web_search`, `image_generation`, `translation_automatic_translation`), plus utility tools:

- `list_models`: providers, models and pricing, optionally for one tool (`{"tool": "web_search"}`).
- `upload_file`: upload local content (base64) and get a file ID for any file parameter.
- `check_job`: status and result of a long-running tool's job.
- `list_voices`: values for a `voice` parameter.
- `manage_collections`: build a collection for tools that take a `collection_id`.

Discover the live catalog with `tools/list`; every tool has a JSON Schema for its parameters.

## Conventions

- `model` selects the provider: `"provider"` or `"provider/model"`, e.g. `"amazon"` or `"firecrawl"`. Call `list_models` for exact names and prices.
- File parameters take a public URL or a file ID from `upload_file`.
- Long-running tools (their description says so) return a job ID: poll `check_job` until `status` is `success` or `fail`.
- Failed calls come back as tool results with `is_error` set: pass the error text back to the model so it can correct its arguments.
- OCR and scraping output can be large: truncate tool results (about 20,000 characters) before adding them to the conversation.

## Without an MCP client

Fetch the catalog with an MCP SDK, pass the tool schemas to any function-calling model on the Eden AI gateway (`https://api.edenai.run/v3`) and execute the calls the model asks for. Full Python loops (OpenAI and Anthropic SDKs): https://www.edenai.co/docs/v3/expert-models/mcp-server
