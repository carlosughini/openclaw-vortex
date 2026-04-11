# Mission Control Setup & Integration Design

**Date:** 2026-04-06  
**Status:** Approved  
**Scope:** Deploy OpenClaw Mission Control with remote + local access, cost tracking, project management, and approval workflows.

---

## Overview

Mission Control serves as centralized operations hub for:
- Multi-agent orchestration
- Approval workflows & governance
- Remote gateway management
- Project management
- API cost tracking (OpenRouter)
- UI theme toggle (light/dark)

---

## Architecture

### Components

**Frontend:** Next.js (port 3000)
- Dashboard for agents, projects, approvals
- Cost tracker with manual refresh
- Theme toggle (light/dark)

**Backend:** FastAPI (port 8000)
- REST API for projects, approvals, cost sync
- Gateway integration (read-only)
- OpenRouter cost pulls

**Database:** PostgreSQL (compose-managed)
- Projects, approvals, user preferences
- Theme preference per user

**External Services:**
- OpenClaw Gateway (queries on-demand)
- OpenRouter API (costs on manual trigger)

---

## Access Control

**Local Access:**
- IP whitelist (e.g., 127.0.0.1, 192.168.x.x)
- No auth required

**Remote Access:**
- Auth token (stored in .env)
- Token validated on every request

---

## Data Flow

### Agent Status Query
```
User opens dashboard
→ Frontend requests /agents from Backend
→ Backend queries OpenClaw Gateway (on-demand)
→ Returns live agent state
```

### Cost Tracking
```
User clicks "Refresh Costs"
→ Backend pulls OpenRouter API
→ Parses costs (filtered by your tokens)
→ Stores in PostgreSQL
→ Frontend displays breakdown per project
```

### Project Management
```
User creates project (UI)
→ Backend stores in DB
→ Associates agents/gateways with project
→ Manual tagging for cost allocation
```

---

## Configuration

**Required (.env):**
```
FRONTEND_PORT=3000
BACKEND_PORT=8000
BASE_URL=http://localhost:8000 (or your remote URL)
CORS_ORIGINS=http://localhost:3000 (or your remote origin)
LOCAL_AUTH_TOKEN=<generate 50+ char token>
OPENROUTER_API_KEY=<from .env>
OPENCLAW_GATEWAY_URL=http://localhost:18789
LOCAL_IPS=127.0.0.1,192.168.x.x
```

---

## Theme Toggle

**Storage:** Browser local storage + PostgreSQL (per user)

**Behavior:**
- Default: system preference
- Toggle: persists across sessions
- Applied globally (all pages)

---

## Deployment Steps

1. Clone repo to `/root/.openclaw/mission-control`
2. Generate auth token (50+ chars)
3. Create `.env` from `.env.example`
4. Update `BASE_URL`, `CORS_ORIGINS`, `LOCAL_IPS`, `LOCAL_AUTH_TOKEN`
5. Run `docker-compose up`
6. Access locally: `http://localhost:3000`
7. Access remotely: `https://<your-domain>:3000` (with token header)

---

## Success Criteria

- ✅ Deploy Mission Control (local + remote)
- ✅ Query OpenClaw agents live
- ✅ Pull OpenRouter costs (manual trigger)
- ✅ Create/manage projects
- ✅ Approval workflow UI
- ✅ Theme toggle works
- ✅ Auth (IP whitelist + token) working
