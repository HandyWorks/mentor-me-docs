# Mentor Me

Mentor Me is a mentorship matching app: browse a shared directory of mentors, filter
by role and specialty, set up a rich personal profile, price your sessions, and book
mentoring time with other users.

This repository is the **project documentation** for the Mentor Me program.

| Product | Status |
| --- | --- |
| [iOS App](ios-app/project-overview.md) | In active development |
| [Android App](android-app/README.md) | Documentation published as it is built |
| [Website](website/README.md) | Planned |

This repository intentionally contains **documentation only** — app source code is
maintained in a private repository.

## Documentation style

Each product is documented in standard-operating-procedure (SOP) form: purpose,
prerequisites, numbered steps, verification, and troubleshooting. Documentation is
published incrementally as each product is built.

## Tech stack

- **iOS client:** Swift, SwiftUI, Xcode
- **Backend:** Python, FastAPI, PostgreSQL
- **Networking:** private HTTPS over Tailscale (tailnet-only)
- **CI:** GitHub Actions — simulator build + unit tests on every push and PR
