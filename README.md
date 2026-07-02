# AI Support Agent — n8n Workflow

A production-ready AI customer support endpoint built in n8n, powered by Claude (Anthropic API). Deployed as a webhook API that any website, chat widget, or app can call directly to get instant, on-brand support answers.

## Features

- **Input validation** — rejects malformed requests with a clear `400` error before wasting an API call
- **Scoped AI responses** — a system prompt constrains Claude to only answer questions relevant to the business (orders, shipping, returns, products), politely redirecting off-topic queries to human support
- **Graceful failure handling** — if the AI provider is down or rate-limited, the workflow returns a clean `502` error instead of crashing or hanging
- **Structured JSON in and out** — easy to integrate into any front-end, chat widget, or app

## Architecture

```
Webhook (POST)
   → If (validates "prompt" field exists)
        → false: 400 "Missing required field: prompt"
        → true: Build Prompt → HTTP Request (Claude API)
                                   → error: 502 "AI service temporarily unavailable"
                                   → success: Extract Reply → 200 { "answer": "..." }
```

## Example Request

```bash
curl -X POST https://your-domain.com/webhook/support \
  -H "Content-Type: application/json" \
  -d '{"prompt": "How long does shipping take?"}'
```

## Example Response

```json
{"answer": "Standard shipping typically takes 3-5 business days, while expedited shipping takes 1-2 business days..."}
```

## Example: Off-topic Redirect

```bash
curl -X POST https://your-domain.com/webhook/support \
  -H "Content-Type: application/json" \
  -d '{"prompt": "Can you write me a poem about the moon?"}'
```

```json
{"answer": "I'm sorry, but writing poems is outside of what I'm able to help with here! I'm specialized in assisting with Cherry Electronics orders, shipping, returns, and product questions. For anything else, feel free to reach out to our human support team."}
```

## Tech Stack

- **n8n** — workflow orchestration
- **Claude (Anthropic API)** — natural language understanding and response generation
- **Webhook-based architecture** — framework-agnostic, callable from any front-end

## Setup

1. Import `workflow.json` into your n8n instance
2. Create a Header Auth credential named for the `x-api-key` header, using your Anthropic API key
3. Customize the `system` prompt in the HTTP Request node for your specific business/use case
4. Publish the workflow to activate the production webhook URL

## Customization

This workflow is built as a template — the system prompt, business name, and scope of allowed topics can be swapped for any business needing an AI-powered FAQ/support responder (e-commerce, SaaS, local services, etc).

---

*Built as a demonstration of n8n + AI agent integration for automation freelancing.*