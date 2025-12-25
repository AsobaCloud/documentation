# API Gateway Service

> A lightweight API gateway for routing, authentication, and rate limiting microservices.

---

## Overview

This project provides:
- A centralized entry point for microservice architectures
- JWT-based authentication and authorization
- Configurable rate limiting per endpoint
- Request/response logging and metrics

**Intended for:** DevOps teams managing multiple backend services

**Non-goals:** This is not a service mesh replacement. It handles HTTP/REST only, not gRPC or message queues.

---

## Quick Start

Get up and running in under 5 minutes.

### Prerequisites
- Node.js 18+ or Docker
- Redis (for rate limiting)

### Installation
```bash
npm install -g @asoba/api-gateway
```

### Run

```bash
# Start with default config
api-gateway start

# Or with custom config
api-gateway start --config ./gateway.yaml
```

### Verify

You should see:
```
✓ API Gateway listening on http://localhost:3000
✓ Connected to Redis at localhost:6379
✓ Loaded 5 routes from config
```

Visit `http://localhost:3000/health` - you should get `{"status": "ok"}`.

---

## Usage / Functionality

### Key Features

* Route requests to multiple backend services
* Authenticate requests using JWT tokens
* Apply rate limits (per IP, per user, per endpoint)
* Collect metrics and logs in JSON format

### Examples

```bash
# Make an authenticated request
curl -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  http://localhost:3000/api/users

# Check rate limit status
curl -i http://localhost:3000/api/data
# See X-RateLimit-Remaining header
```

---

## Configuration

### Environment Variables

| Variable | Description      | Default |
| -------- | ---------------- | ------- |
| PORT | Gateway listen port | 3000 |
| REDIS_URL | Redis connection string | redis://localhost:6379 |
| JWT_SECRET | Secret for JWT verification | (required) |
| LOG_LEVEL | Logging verbosity | info |

### Config Files

`gateway.yaml` defines routes and policies:

```yaml
routes:
  - path: /api/users
    target: http://users-service:8080
    methods: [GET, POST]
    rateLimit: 100/minute
    auth: required
```

---

## Contributing & Reporting Issues

We welcome contributions and feedback.

### Reporting Bugs

* Describe the issue clearly
* Include steps to reproduce
* Include logs or screenshots if relevant
* Tag with `bug` label

### Feature Requests

* Explain the problem you're trying to solve
* Describe the proposed solution
* Tag with `enhancement` label

### Contributing Code

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Add tests where applicable
4. Submit a pull request

---

## Troubleshooting

### Common Issues

**Problem:** `Error: Cannot connect to Redis`
**Solution:** Ensure Redis is running (`redis-server`) and the REDIS_URL is correct.

**Problem:** `401 Unauthorized` on valid JWT
**Solution:** Verify JWT_SECRET matches the one used to sign tokens.

**Problem:** Routes not loading
**Solution:** Check `gateway.yaml` syntax with `api-gateway validate --config ./gateway.yaml`

---

## Support / Contact

For questions, bug reports, or feature requests:

📧 **[shingai@asoba.co](mailto:shingai@asoba.co)**

---

## License

MIT License - see LICENSE file for details.
