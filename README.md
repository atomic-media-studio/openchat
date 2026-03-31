# openchat

Cross-platform desktop chat for HCI and cognitive science workflows.

## Overview

- Native application with UI and theming,
- **Ollama** integration: model selection, optional token limits, and local inference (no cloud).
- OpenAI-compatible **HTTP API** on `127.0.0.1:3000` for inbound agent and evaluator messages, health checks.
- **Audit trail**: append-only JSONL (`openchat-audit.jsonl`).
- **SQLite** (`openchat.db` in the working directory): durable conversations, generation metadata, and session settings.
- **Import/Export**: chat and keyboard logs as JSON or CSV, agent templates as JSON; 

## Build

```sh
# Development: Builds to 'target/debug/'
cargo run

# Distribution: Builds to 'target/release/'
cargo build --release
```

## API

All endpoints are local-only (bind address in code). Inference for `/v1/*` uses Ollama at `127.0.0.1:11434`.

- `GET /health` — returns `OK` when the server is enabled.
- `POST /` with plain text — message attributed to `API` (ingest-only for Ollama unless you opt in; see below).
- `POST /` with conversation JSON — `sender_name` and `message` (plus optional metadata).
- `POST /` with evaluator JSON — `evaluator_name`, `sentiment`, and `message`.

- **OpenAI-compatible (Ollama):** `GET /v1/models` lists local models. `POST /v1/chat/completions` accepts `model`, `messages` (`role` / `content`), optional `max_tokens` (maps to Ollama `num_predict`), `temperature`, `seed`. Streaming is not supported yet (`stream: true` returns 400).
- Example: `curl -X POST http://127.0.0.1:3000/ -H "Content-Type: application/json" -d '{"sender_name":"Agent 1","message":"Hi","sender_id":1,"receiver_id":0,"receiver_name":"UI","topic":"chat","timestamp":"11:44:50"}'`
