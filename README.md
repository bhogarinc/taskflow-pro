# TaskFlow Pro 🚀

**Enterprise task management with real-time collaboration, team workspaces, and WebSocket notifications.**

[![CI/CD](https://github.com/bhogarinc/taskflow-pro/actions/workflows/ci-cd.yml/badge.svg)](https://github.com/bhogarinc/taskflow-pro/actions)
[![Coverage](https://codecov.io/gh/bhogarinc/taskflow-pro/branch/main/graph/badge.svg)](https://codecov.io/gh/bhogarinc/taskflow-pro)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 📋 Table of Contents

- [Overview](#overview)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Quick Start](#quick-start)
- [API Documentation](#api-documentation)
- [Development](#development)
- [Deployment](#deployment)
- [Monitoring](#monitoring)

---

## 🎯 Overview

TaskFlow Pro is a modern, scalable task management platform designed for teams. It combines intuitive task organization with powerful real-time collaboration features.

### Key Features

| Feature | Description |
|---------|-------------|
| 🔐 **Authentication** | JWT-based auth with OAuth2, password reset, email verification |
| 📝 **Task Management** | Full CRUD with priorities, due dates, labels, attachments |
| 👥 **Team Collaboration** | Workspaces, member invites, role-based permissions |
| 🔔 **Real-time Updates** | WebSocket-powered live notifications and task updates |
| 📊 **Dashboard & Analytics** | Progress tracking, team productivity metrics |
| 🔍 **Search & Filter** | Full-text search, advanced filtering, saved views |

---

## 🛠 Tech Stack

### Backend
- **Framework**: Python 3.11 + FastAPI
- **Database**: PostgreSQL 15 + SQLAlchemy 2.0
- **Cache**: Redis 7 (WebSocket pub/sub + caching)
- **Auth**: JWT tokens, bcrypt password hashing
- **WebSocket**: Native FastAPI WebSocket with Redis pub/sub
- **Testing**: pytest, pytest-asyncio, httpx

### Frontend
- **Framework**: React 18 + TypeScript
- **State**: Zustand + TanStack Query
- **UI**: Tailwind CSS + shadcn/ui
- **Routing**: React Router v6
- **Real-time**: Socket.io-client
- **Testing**: Vitest + React Testing Library + Playwright

### Infrastructure
- **Containerization**: Docker + Docker Compose
- **Orchestration**: Kubernetes (EKS/GKE)
- **CI/CD**: GitHub Actions
- **Monitoring**: Prometheus + Grafana + Loki
- **Logging**: Structured JSON logging

---

## 🏗 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        Nginx Ingress                         │
│              (SSL termination, rate limiting)                │
└───────────────────────┬─────────────────────────────────────┘
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│   Frontend   │ │   REST API   │ │  WebSocket   │
│   (React)    │ │   (FastAPI)  │ │   Server     │
└──────┬───────┘ └──────┬───────┘ └──────┬───────┘
       │                │                │
       └────────────────┼────────────────┘
                        │
              ┌─────────┴─────────┐
              ▼                   ▼
       ┌─────────────┐    ┌─────────────┐
       │ PostgreSQL  │    │    Redis    │
       │   (Tasks)   │    │  (Pub/Sub)  │
       └─────────────┘    └─────────────┘
```

---

## 🚀 Quick Start

### Prerequisites

- Docker & Docker Compose
- Node.js 20+ (for local frontend dev)
- Python 3.11+ (for local backend dev)

### Using Docker Compose (Recommended)

```bash
# Clone the repository
git clone https://github.com/bhogarinc/taskflow-pro.git
cd taskflow-pro

# Start all services
docker-compose up -d

# Run database migrations
docker-compose exec backend alembic upgrade head

# Create initial admin user
docker-compose exec backend python scripts/create_admin.py

# Access the application
# Frontend: http://localhost:3000
# API Docs: http://localhost:8000/docs
```

### Local Development

```bash
# Backend
cd backend
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000

# Frontend (new terminal)
cd frontend
npm install
npm run dev
```

---

## 📚 API Documentation

### Authentication Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/auth/register` | User registration |
| POST | `/api/v1/auth/login` | User login (returns JWT) |
| POST | `/api/v1/auth/refresh` | Refresh access token |
| POST | `/api/v1/auth/password-reset` | Request password reset |
| POST | `/api/v1/auth/password-reset/confirm` | Confirm password reset |

### Task Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/tasks` | List tasks (with filters) |
| POST | `/api/v1/tasks` | Create new task |
| GET | `/api/v1/tasks/{id}` | Get task details |
| PUT | `/api/v1/tasks/{id}` | Update task |
| DELETE | `/api/v1/tasks/{id}` | Delete task |
| POST | `/api/v1/tasks/{id}/assign` | Assign task to user |

### Team Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/teams` | List user's teams |
| POST | `/api/v1/teams` | Create team |
| GET | `/api/v1/teams/{id}` | Get team details |
| POST | `/api/v1/teams/{id}/invite` | Invite member |
| DELETE | `/api/v1/teams/{id}/members/{user_id}` | Remove member |

### WebSocket Events

| Event | Direction | Description |
|-------|-----------|-------------|
| `task:created` | Server → Client | New task created |
| `task:updated` | Server → Client | Task was updated |
| `task:deleted` | Server → Client | Task was deleted |
| `notification:new` | Server → Client | New notification |
| `user:typing` | Bidirectional | Typing indicator |

**Full API documentation**: Available at `/docs` (Swagger UI) or `/redoc` (ReDoc) when running the backend.

---

## 💻 Development

### Project Structure

```
taskflow-pro/
├── backend/                 # FastAPI backend
│   ├── app/
│   │   ├── api/            # API routes
│   │   ├── core/           # Config, security, logging
│   │   ├── models/         # SQLAlchemy models
│   │   ├── schemas/        # Pydantic schemas
│   │   ├── services/       # Business logic
│   │   └── websocket/      # WebSocket handlers
│   ├── tests/              # Unit & integration tests
│   ├── alembic/            # Database migrations
│   └── Dockerfile
├── frontend/               # React frontend
│   ├── src/
│   │   ├── components/     # React components
│   │   ├── pages/          # Page components
│   │   ├── store/          # Zustand stores
│   │   ├── hooks/          # Custom hooks
│   │   └── lib/            # Utilities
│   ├── tests/
│   └── Dockerfile
├── infrastructure/         # DevOps configs
│   ├── docker/
│   ├── kubernetes/
│   └── terraform/
└── docs/                   # Documentation
```

### Running Tests

```bash
# Backend tests
cd backend
pytest -v --cov=app --cov-report=html

# Frontend unit tests
cd frontend
npm run test

# E2E tests
cd frontend
npm run test:e2e
```

### Code Quality

```bash
# Backend linting
cd backend
ruff check .
black .
mypy app/

# Frontend linting
cd frontend
npm run lint
npm run format
```

---

## 🚢 Deployment

### Environments

| Environment | URL | Branch |
|-------------|-----|--------|
| Development | http://dev.taskflow.bhogarinc.com | `develop` |
| Staging | https://staging.taskflow.bhogarinc.com | `release/*` |
| Production | https://taskflow.bhogarinc.com | `main` |

### Deployment Process

1. **Development**: Auto-deploy on push to `develop`
2. **Staging**: Create release branch, auto-deploy
3. **Production**: Merge to `main`, requires approval

### Kubernetes Deployment

```bash
# Apply configurations
kubectl apply -f infrastructure/kubernetes/namespace.yaml
kubectl apply -f infrastructure/kubernetes/configmap.yaml
kubectl apply -f infrastructure/kubernetes/secrets.yaml
kubectl apply -f infrastructure/kubernetes/postgres.yaml
kubectl apply -f infrastructure/kubernetes/redis.yaml
kubectl apply -f infrastructure/kubernetes/backend.yaml
kubectl apply -f infrastructure/kubernetes/frontend.yaml
kubectl apply -f infrastructure/kubernetes/ingress.yaml
```

---

## 📊 Monitoring

### Metrics

- **Application**: Request latency, error rates, throughput
- **Database**: Query performance, connection pool, slow queries
- **WebSocket**: Active connections, message throughput
- **Infrastructure**: CPU, memory, disk, network

### Alerts

| Alert | Condition | Severity |
|-------|-----------|----------|
| High Error Rate | > 5% 5xx errors | Critical |
| High Latency | p95 > 500ms | Warning |
| DB Connection Pool | > 80% utilized | Warning |
| WebSocket Disconnections | > 10% drop rate | Warning |

### Dashboards

Access Grafana at: `https://grafana.bhogarinc.com/d/taskflow`

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'feat: add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

---

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file.

---

## 🆘 Support

- **Issues**: [GitHub Issues](https://github.com/bhogarinc/taskflow-pro/issues)
- **Discussions**: [GitHub Discussions](https://github.com/bhogarinc/taskflow-pro/discussions)
- **Email**: support@bhogarinc.com

---

Built with ❤️ by the BhogarInc Engineering Team
