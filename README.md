# Intent + LLM Chatbot

A deployable Flask chatbot that routes conversations through a trained intent classifier and can use an LLM-backed response path when `GROQ_API_KEY` is configured.

## Architecture

```text
Message → preprocessing → intent classifier → intent response
                                  └──────────→ optional LLM response
```

The repository separates training code, inference logic, the Flask application, and container deployment. Large model artifacts stay outside the Docker image and can be downloaded when the container starts.

## Run locally

```bash
git clone https://github.com/tominister/intent-llm-chatbot.git
cd intent-llm-chatbot
python -m venv .venv
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
python app.py
```

Open `http://127.0.0.1:5000`.

## Model and response paths

- The intent model, tokenizer, and label encoder support local classification.
- `GROQ_API_KEY` enables the optional LLM-backed response path.
- `MODEL_STORAGE_URL` can point to remotely hosted model artifacts that the container downloads at startup.

## Container deployment

The included Docker configuration excludes `model/`, `data/`, and local performance artifacts to keep images small. For development with a locally mounted model:

```bash
docker compose up --build
```

The app exposes `/health`, returning `200` when model dependencies are available and `503` while they are unavailable. Store production secrets in the deployment platform's secret manager rather than committing `.env` files.

## Limitations

- Response quality depends on the intent training data and the optional external LLM provider.
- Production deployments should add authentication, rate limiting, monitoring, and provider-failure handling.
