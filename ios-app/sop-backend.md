# SOP — Backend API & Database Setup

**Purpose:** Stand up the FastAPI + PostgreSQL backend the iOS app talks to.
**Scope:** Local API on the developer Mac, private HTTPS exposure, security rules.

## Prerequisites

- Python 3.11+
- PostgreSQL with a database and a user able to manage the `mentors` table
- Tailscale signed in on the Mac (and on any device that should reach the API)
- The `backend/` directory from the source repository

## Steps

1. Create a virtual environment and install dependencies:
   ```sh
   cd backend
   python3 -m venv .venv
   source .venv/bin/activate
   python -m pip install -r requirements.txt
   ```
2. Configure the environment:
   ```sh
   cp .env.example .env
   ```
   Edit `.env` with your database connection URL. **`.env` is private and never
   committed.**
3. Apply the schema (idempotent — safe to re-run; it also includes the
   incremental column migrations):
   ```sh
   psql "$DATABASE_URL" -f schema.sql
   ```
4. Start the API (binds to `127.0.0.1:8000`):
   ```sh
   python main.py
   ```
5. Expose it as private HTTPS for the tailnet:
   ```sh
   tailscale serve --bg http://127.0.0.1:8000
   tailscale serve status
   ```
   The endpoint shown by `tailscale serve status` is what clients use.
6. Sign client devices (e.g. iPhone) into the same tailnet. The app is
   preconfigured with the endpoint and connects automatically — no URL or token
   entry in the UI.

## Security rules (non-negotiable)

- The API binds to **localhost only** — never `0.0.0.0`.
- The Tailscale Serve endpoint is **tailnet-only** — never Tailscale Funnel, never
  a public port.
- Restrict tailnet access to trusted devices and users with Tailscale access
  controls.
- Keep `.env` out of version control.
- Passwords are stored as **argon2id hashes** in Postgres, never in raw form.
  Bearer tokens are stored hashed; raw tokens live only in the client's Keychain.

## API surface

| Method | Path | Purpose |
| --- | --- | --- |
| GET | `/health` | Liveness probe |
| GET | `/api/capabilities` | Feature flags for the client |
| GET | `/api/mentors` | List mentors |
| POST | `/api/mentors` | Create mentor entry |
| PUT | `/api/mentors/{id}` | Update mentor entry |
| DELETE | `/api/mentors/{id}` | Delete mentor entry |
| POST | `/api/auth/register` | Create account (email + password, role) |
| POST | `/api/auth/login` | Obtain bearer token |
| POST | `/api/auth/logout` | Revoke token |
| GET / PUT | `/api/me` | Read / update own profile |
| DELETE | `/api/me` | Delete account |

## Data model

- **`mentors`** — shared directory: name, role, focus, tags, rating, session
  count, bio, and optional price columns for preset durations plus one custom
  duration/price pair.
- **`users` / `user_details` / `api_tokens`** — accounts, profile details (goals,
  offers, availability, privacy), and hashed bearer tokens.
- Schema changes are applied by **idempotent startup migrations** in the API —
  new columns are added without touching existing rows.

## Verification

- `curl -s https://<tailnet-endpoint>/health` returns OK
- Register and sign in from the app on a device on the tailnet
- Create a mentor from the app; the row appears in the `mentors` table
