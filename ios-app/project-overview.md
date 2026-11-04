# iOS App — Project Overview

## Purpose

Mentor Me helps people find and connect with mentors. The iOS app lets a user sign
in as a **mentor** or a **mentee**, browse the shared mentor directory, build a
detailed profile, publish session prices, and book mentoring sessions.

## Features (shipped)

- **Onboarding** — sign in or create an account, choose mentor/mentee role
- **Accounts** — email + password authentication against Postgres, bearer tokens
  stored in the iOS Keychain, argon2id password hashing server-side
- **Profiles** — name, location, goals, offers, availability, and privacy settings
  all functional and synced
- **Mentor directory** — search plus role filtering with a job/specialty taxonomy,
  ratings, and session counts
- **Session pricing** — preset durations (15/30/45/60 min) plus one custom duration
  with its own price
- **Booking** — end-to-end session booking from the app
- **Account deletion** — self-service delete with confirmation
- **Session re-auth** — dead/expired sessions land on sign-in instead of stale data
- **App icon** — forest/lime serif "M" monogram (1024×1024)

## Architecture

- **Client:** SwiftUI app in a single Xcode project with dedicated unit-test and
  UI-test targets. API clients, Keychain storage, and role catalog are small,
  focused modules.
- **Backend:** FastAPI service with PostgreSQL. Idempotent startup migrations keep
  the schema in sync without touching existing data.
- **Networking:** the API binds to localhost and is exposed as private HTTPS via
  Tailscale Serve. Access is limited to devices on a trusted tailnet; no public
  port is ever exposed. The app connects automatically — no URL or token entry in
  the UI.
- **Offline behavior:** local cache with a graceful "database unavailable" fallback;
  changes are kept on-device and sync when reachable.

## Current status

Active development. The app builds and passes its unit-test suite in continuous
integration on both `main` and `dev`.

## Next

- Remote sync of booked sessions and messages (currently on-device)
- Payments and customer billing
- App Store readiness (including the sign-in options required for public
  distribution)
- Android port and website
