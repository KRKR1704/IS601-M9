# Title: "Reflection — Assignment 9"
# Author: Roopesh Kumar Reddy Kaipa
# Date: 2025-11-03
---

I worked on a FastAPI + Postgres development stack and hit two main issues: (1) a Postgres on-disk layout mismatch when using newer images (the named volume lacked `PG_VERSION`), which I addressed by choosing to pin the image to `postgres:17` or recreate/backup the volume as appropriate; and (2) dependency conflicts while fixing Trivy findings (notably `h11` and `starlette`) that required careful dependency resolution or upgrading `fastapi`.

Next steps are simple: back up any important DB data before changing images, pick a dependency strategy (upgrade FastAPI to match fixed Starlette, or temporarily pin Starlette to a compatible version), and re-run tests and the vulnerability scan to confirm the fixes.
---