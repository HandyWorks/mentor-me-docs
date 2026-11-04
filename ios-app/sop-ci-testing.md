# SOP — CI & Testing

**Purpose:** Describe the continuous-integration pipeline that builds and tests the
iOS app on every push and pull request.
**Scope:** What runs, where it runs, and how to reproduce it locally.

## Pipeline

- **Trigger:** push or pull request targeting `main` or `dev`
- **Runner:** macOS (Apple Silicon) with Xcode 27
- **Timeout:** 25 minutes
- **Jobs:**
  1. **Build** the app for the iPhone simulator (Debug, code signing disabled)
  2. **Run the unit tests** on an available iPhone simulator

## Local reproduction

The same two commands the pipeline runs:

```sh
# Build
xcodebuild build \
  -project "Mentor me.xcodeproj" \
  -scheme "Mentor me" \
  -configuration Debug \
  -sdk iphonesimulator \
  -derivedDataPath DerivedData \
  CODE_SIGNING_ALLOWED=NO

# Unit tests (pick any available simulator name)
xcodebuild test \
  -project "Mentor me.xcodeproj" \
  -scheme "Mentor meTests" \
  -destination "platform=iOS Simulator,name=iPhone 17" \
  -derivedDataPath DerivedData \
  CODE_SIGNING_ALLOWED=NO
```

## Reading CI results

- Green check on the commit/PR → build and tests passed.
- Failures include the full `xcodebuild` log: look for compile errors (build
  stage) or failing test names (test stage).
- "No available iPhone simulator" means the runner lacked a booted device —
  retry the workflow; persistent failures warrant checking the workflow's
  device-selection step.

## Verification

Push a trivial change on a branch targeting `dev`; the workflow starts
automatically and must report build + tests passing before the branch is merged.
