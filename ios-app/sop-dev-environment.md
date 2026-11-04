# SOP — iOS Development Environment Setup

**Purpose:** Get a machine ready to build and run the Mentor Me iOS app.
**Scope:** Developer workstation setup, project open, run, and test.

## Prerequisites

- macOS on Apple Silicon (arm64) with Xcode 27 (or a current Xcode release with
  iOS simulators)
- `git`
- A GitHub account with access to the (private) source repository

## Steps

1. Clone the source repository:
   ```sh
   git clone git@github.com:HandyWorks/Mentor-Me.git
   cd Mentor-Me
   ```
2. Open the project in Xcode:
   ```sh
   open "Mentor me.xcodeproj"
   ```
3. Select the **Mentor me** scheme and an iPhone simulator as the destination.
4. Run the app (⌘R). The first build may take a few minutes.
5. Run the unit tests (⌘U) — this uses the **Mentor meTests** scheme.
6. (Optional) The **Mentor meUITests** target contains UI tests.

## Project layout

| Path | Contents |
| --- | --- |
| `Mentor me/` | App sources: SwiftUI views, API clients, Keychain store, role catalog |
| `Mentor meTests/` | Unit tests |
| `Mentor meUITests/` | UI tests |
| `backend/` | FastAPI + PostgreSQL API (see *SOP — Backend API & Database Setup*) |
| `.github/workflows/ci.yml` | CI pipeline (see *SOP — CI & Testing*) |

The app expects the backend API to be reachable (see the backend SOP). If it is
not, the app shows its offline fallback and keeps working locally.

## Verification

- App launches in the simulator and shows the onboarding screen
- `xcodebuild test` against the test scheme exits 0

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| No iPhone simulator available | `xcrun simctl list devices available` — install one via Xcode ▸ Settings ▸ Component Downloads |
| Signing errors on a physical device | Use a simulator, or add the device to your Xcode signing team |
| App stuck on "database unavailable" | The backend is not running or the device is off the tailnet — see the backend SOP |
