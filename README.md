# Polar Ops

**Gamified, role-based simulation of Antarctic research station logistics and asset management.**

Built for Smart India Hackathon 2026 — Problem **SIH26062** · Team **Polar Forge**

## Quick Start

### 1. Backend (FastAPI + SQLite)

```bash
cd backend
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS/Linux
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

API docs: http://localhost:8000/docs

### 2. Frontend (React + Vite + Tailwind)

```bash
cd frontend
npm install
npm run dev
```

App: http://localhost:5173

## Demo Flow

1. Open **3 browser tabs** — join as each role (Cargo Manager, Energy Engineer, Emergency Responder)
2. Explore role-specific panels and complete tasks
3. Click **"Trigger Blizzard Incoming"** on any dashboard
4. Coordinate during the 2-minute blizzard countdown:
   - **Energy Engineer** — boost heating allocation to ≥45%
   - **Emergency Responder** — resolve hypothermia incident (consumes medical kits)
   - **Cargo Manager** — secure inventory (QR scans blocked during blizzard)
5. When timer ends → **Mission Outcome** screen shows pass/fail based on readiness score

## Readiness Score Formula

Implemented in `backend/scenario_engine.py`:

```
readiness_score = w1 × inventory_sufficiency
                + w2 × fuel_buffer
                + w3 × (1 − delay_factor)
                + w4 × crew_health_pct
```

| Weight | Component | Default |
|--------|-----------|---------|
| w1 | Inventory above minimum thresholds | 0.30 |
| w2 | Fuel reserve buffer | 0.25 |
| w3 | On-schedule operations (inverse delay) | 0.20 |
| w4 | Crew health & morale | 0.25 |

**Pass threshold:** 60/100

## Architecture

```
backend/
  main.py              — FastAPI routes + WebSocket broadcast
  models.py            — SQLAlchemy models (Station, Inventory, Player, Task, Event)
  scenario_engine.py   — Blizzard event + readiness calculation
  seed.py              — Bharati Station seed data

frontend/
  src/components/      — Join, Dashboard, role panels, Mission Outcome
  src/hooks/           — useStationState (WebSocket + 2s polling fallback)
  src/api.ts           — REST client
```

## Real-time Updates

- Primary: WebSocket at `/ws` — server broadcasts state on every mutation
- Fallback: 2-second polling if WebSocket fails

## PWA

The frontend includes a basic PWA manifest and service worker via `vite-plugin-pwa`. Installable on mobile for demo purposes.

## PostgreSQL Migration Path

Change `DATABASE_URL` in `backend/database.py`:

```python
DATABASE_URL = "postgresql://user:pass@localhost/polar_ops"
```

Remove `connect_args={"check_same_thread": False}` for PostgreSQL.

## License

Prototype for SIH26062 hackathon demo — Team Polar Forge.
