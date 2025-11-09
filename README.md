# LMGuard Backend

AI-powered tutoring system with built-in safety guardrails. This FastAPI backend provides secure, policy-driven chat interactions with real-time content scanning and agentic decision-making.

## Features

- **Multi-Scanner Content Analysis**: Detects toxicity, PII, injection attacks, and dishonesty
- **Policy Engine**: Configurable YAML-based policy system for content moderation
- **Agentic Guard**: AI-powered decision-making for borderline cases
- **Audit Logging**: Comprehensive audit trail for all interactions
- **Prometheus Metrics**: Built-in metrics endpoint for monitoring
- **Gemini Integration**: Google Gemini-powered tutoring responses
- **Docker Support**: Containerized deployment ready

## Tech Stack

- **Framework**: FastAPI
- **Python**: 3.10+
- **AI/ML**: Google Gemini, OpenAI (for embeddings)
- **Monitoring**: Prometheus metrics
- **Validation**: Pydantic, JSON Schema

## Prerequisites

- Python 3.10 or higher
- pip
- Docker (optional, for containerized deployment)

## Installation

### Local Development

1. Clone the repository:
```bash
git clone <repository-url>
cd LMGuard-backend
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Create a `.env` file in the root directory:
```env
OPENAI_KEY=your_openai_api_key
GOOGLE_API_KEY=your_google_api_key
POLICY_FILE=config/policy.yaml
AGENT_TIMEOUT_SECONDS=1.0
CACHE_MAX_ITEMS=1000
EMBEDDING_PROVIDER=GOOGLE
LLM_PROVIDER=GOOGLE
LOG_FILE=data/lmguard_audit.json
HASH_SALT=your-secret-salt-change-in-prod
ADMIN_TOKEN=your-admin-token
ALLOWED_ORIGINS=*
```

5. Run the application:
```bash
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`

### Docker Deployment

1. Build the Docker image:
```bash
docker build -t lmguard-backend .
```

2. Run with Docker Compose:
```bash
docker-compose up
```

Or run directly:
```bash
docker run -p 8000:8000 --env-file .env lmguard-backend
```

## API Endpoints

### Health Check
```
GET /health
```
Returns the health status of the service.

### Metrics
```
GET /metrics
```
Returns Prometheus-formatted metrics.

### Chat
```
POST /chat
```
Main chat endpoint with guard logic.

**Request Body:**
```json
{
  "messages": [
    {
      "role": "user",
      "content": "Your message here"
    }
  ],
  "student_id": "optional-student-id"
}
```

**Response:**
```json
{
  "action": "allow|block|redact|rewrite",
  "output": "Response text",
  "policy_reason": "Explanation of policy decision",
  "agent_confidence": 0.95
}
```

## Project Structure

```
LMGuard-backend/
├── action_app/          # Action application logic
├── agentic_guard/       # AI-powered guard agent
├── audit/              # Audit logging
├── config/             # Configuration files (policy.yaml)
├── data/               # Data storage
├── metrics/            # Prometheus metrics
├── policy/             # Policy engine
├── scanners/           # Content scanners (toxicity, PII, etc.)
├── tutor/              # Gemini tutor adapter
├── tests/              # Test suite
├── main.py             # FastAPI application entry point
├── schemas.py          # Pydantic models
├── deps.py             # Configuration and dependencies
├── requirements.txt    # Python dependencies
├── Dockerfile          # Docker configuration
└── docker-compose.yml  # Docker Compose configuration
```

## Configuration

### Policy Configuration

Edit `config/policy.yaml` to customize content moderation policies. The policy engine evaluates scanner signals and determines actions (allow, block, redact, rewrite, borderline).

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `OPENAI_KEY` | OpenAI API key for embeddings | Required |
| `GOOGLE_API_KEY` | Google API key for Gemini | Required |
| `POLICY_FILE` | Path to policy YAML file | `config/policy.yaml` |
| `AGENT_TIMEOUT_SECONDS` | Agent decision timeout | `1.0` |
| `CACHE_MAX_ITEMS` | Maximum cache items | `1000` |
| `EMBEDDING_PROVIDER` | Embedding provider (OPENAI/GOOGLE/LOCAL) | `GOOGLE` |
| `LLM_PROVIDER` | LLM provider | `GOOGLE` |
| `LOG_FILE` | Audit log file path | `/data/lmguard_audit.json` |
| `HASH_SALT` | Salt for hashing sensitive data | Required |
| `ADMIN_TOKEN` | Admin authentication token | Optional |
| `ALLOWED_ORIGINS` | CORS allowed origins (comma-separated) | `*` |

## Testing

Run the test suite:
```bash
pytest
```

Run with coverage:
```bash
pytest --cov=. --cov-report=html
```

## Development

### Code Structure

- **Scanners**: Content analysis modules in `scanners/`
- **Policy Engine**: Policy evaluation in `policy/engine.py`
- **Agentic Guard**: AI decision-making in `agentic_guard/agent.py`
- **Actions**: Action application in `action_app/actions.py`

### Adding New Scanners

1. Create a new scanner module in `scanners/`
2. Implement the scanner function
3. Register it in `scanners/run_all.py`

### Modifying Policies

Edit `config/policy.yaml` to adjust thresholds and actions. The policy engine will automatically reload on startup.

## Deployment

### Render.com

The project includes a `render.yaml` configuration file for easy deployment to Render.com.

### Manual Deployment

1. Set environment variables on your hosting platform
2. Ensure Python 3.10+ is available
3. Install dependencies: `pip install -r requirements.txt`
4. Run: `uvicorn main:app --host 0.0.0.0 --port $PORT`

## Monitoring

Access Prometheus metrics at `/metrics` endpoint. Key metrics include:
- `agent_calls_total`: Total agent decision calls
- `agent_failures_total`: Agent decision failures
- `scanner_latency`: Scanner execution latency

## Security Considerations

- Never commit `.env` files or API keys
- Use strong `HASH_SALT` values in production
- Configure `ALLOWED_ORIGINS` appropriately
- Regularly review audit logs
- Keep dependencies updated

## License

[Add your license here]

## Contributing

[Add contribution guidelines here]

## Support

[Add support/contact information here]

