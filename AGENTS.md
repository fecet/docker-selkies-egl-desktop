# Repository Guidelines

## Project Structure & Module Organization
- Root contains all sources for the container image.
- `Dockerfile` — builds KDE Plasma + Selkies/KasmVNC; `DISTRIB_RELEASE` supports `20.04`, `22.04`, `24.04`.
- `docker-compose.yml` — local runtime (GPU, port `8080`, optional TURN examples).
- `egl.yml` — Kubernetes Deployment for cluster use.
- `entrypoint.sh`, `kasmvnc-entrypoint.sh`, `selkies-gstreamer-entrypoint.sh` — startup/runtime logic.
- `supervisord.conf` — lightweight service management.
- `.github/workflows/container-publish.yml` — CI builds/pushes multi-OS tags.

## Build, Test, and Development Commands
- Build local image (choose OS):
  - `docker build -t nvidia-egl-desktop:dev --build-arg DISTRIB_RELEASE=24.04 .`
- Run directly (quick test):
  - `docker run --name egl -it -d --gpus 1 -p 8080:8080 -e PASSWD=mypasswd ghcr.io/selkies-project/nvidia-egl-desktop:latest`
- Compose up/down (edit `image:` to your tag to test):
  - `docker compose up -d` / `docker compose down`
- Kubernetes apply:
  - `kubectl create -f egl.yml`

## Coding Style & Naming Conventions
- Shell: `bash`; keep `#!/bin/bash` and `set -e` (prefer `set -euo pipefail` for new code).
- Indentation: 2 spaces. Functions `lower_snake_case`; environment variables `UPPER_SNAKE_CASE`.
- Filenames: kebab-case for scripts (e.g., `kasmvnc-entrypoint.sh`).
- Lint: run `shellcheck` on all `*.sh` before submitting.

## Testing Guidelines
- No unit tests; validate by building all supported Ubuntu tags and starting a container.
- Verify web UI at `http://localhost:8080`, login works, and GPU availability (`nvidia-smi` in the container).
- For TURN/HTTPS/networking changes, use examples in `docker-compose.yml`/`egl.yml` and confirm connectivity.

## Commit & Pull Request Guidelines
- Use Conventional Commits: `feat:`, `fix:`, `chore:`, `ci:`, `docs:`, `refactor:`.
- PRs must include: purpose/impact, tested OS version(s) and GPU, exact run/build commands used, and any updates to `README.md`, `docker-compose.yml`, or `egl.yml`.
- Link issues; include screenshots or logs for UX/networking changes.

## Security & Configuration Tips
- Do not commit secrets. Use env vars and Kubernetes Secrets (e.g., `kubectl create secret generic my-pass --from-literal=my-pass=YOUR_PASSWORD`).
- If using `hostNetwork` or exposing ports publicly, enable auth (`SELKIES_ENABLE_BASIC_AUTH=true`) and review firewall rules.

