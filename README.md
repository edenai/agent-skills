# Eden AI agent skills

Skills that teach coding agents (Claude Code, Codex, Cursor, Cline, OpenCode and others) how to use [Eden AI](https://www.edenai.co): one OpenAI-compatible API to around 1,000 LLMs and expert models from 80+ providers, with provider routing, fallbacks, a European endpoint, cost tracking and a hosted MCP server.

| Skill | Use it when |
| --- | --- |
| [`eden-ai-llm-gateway`](skills/eden-ai-llm-gateway/SKILL.md) | Calling LLMs from many providers with one key, routing by cost / speed / latency, fallbacks |
| [`eden-ai-eu-data-residency`](skills/eden-ai-eu-data-residency/SKILL.md) | Keeping requests in Europe (EU endpoint, HTTP 451 for non-EU models) and zero data retention |
| [`eden-ai-expert-models`](skills/eden-ai-expert-models/SKILL.md) | OCR, document parsing, speech, translation, image analysis, web search, video |
| [`eden-ai-cost-tracking`](skills/eden-ai-cost-tracking/SKILL.md) | Reading the cost of each call, tagging spend, capping prices, batch rates |
| [`eden-ai-mcp-tools`](skills/eden-ai-mcp-tools/SKILL.md) | Connecting an agent to Eden AI's hosted MCP server |

## Install

**Any agent (skills CLI):**

```bash
npx skills add edenai/agent-skills
# or one skill only
npx skills add edenai/agent-skills --skill eden-ai-llm-gateway
```

**Claude Code (plugin, includes the MCP server):**

```
/plugin marketplace add edenai/agent-skills
/plugin install eden-ai@edenai
```

The plugin also configures Eden AI's MCP server (`https://mcp.edenai.run/mcp`). Set your key first:

```bash
export EDENAI_API_KEY=your_key   # get one at https://app.edenai.run/user/register
```

**Manually:** copy a folder from `skills/` into your agent's skills directory (for Claude Code: `~/.claude/skills/`).

## Links

* Documentation: https://www.edenai.co/docs (index for LLMs: https://www.edenai.co/docs/llms.txt)
* API: `https://api.edenai.run/v3` (EU: `https://api.eu.edenai.run/v3`), OpenAPI: https://api.edenai.run/v3/docs/openapi.json
* MCP server: `https://mcp.edenai.run/mcp` ([docs](https://www.edenai.co/docs/v3/expert-models/mcp-server))
* Everything Eden AI offers agents: https://www.edenai.co/.well-known/ai-catalog.json
* The same skills are published at https://www.edenai.co/.well-known/agent-skills/index.json

## License

MIT
