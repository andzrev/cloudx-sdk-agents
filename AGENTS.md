# Repository Guidelines
Contributors build and maintain the Claude Code agents that automate CloudX SDK integration across Android and Flutter. Use this guide to navigate the repo, run validations, and submit consistent improvements.

## Project Structure & Module Organization
- `.claude/agents/<platform>/cloudx-*-<role>.md` holds the agent playbooks invoked in Claude Code as `@agent-cloudx-*-<role>`; keep roles in kebab-case and grouped by platform.
- `docs/<platform>/` contains publisher-facing setup, integration, and orchestration guides that must mirror the agent capabilities.
- `scripts/install.sh` installs the published agent set; update platform arrays or prompts when adding new agents.
- `scripts/<platform>/` hosts validation utilities (API coverage, doc checks) that expect a sibling SDK checkout when run with `SDK_DIR`.
- `SDK_VERSION.yaml` tracks the currently supported SDK release and should be bumped alongside API changes.

## Build, Test, and Development Commands
- `bash scripts/install.sh --platform=android` (or `flutter`) downloads the latest agent prompts for local validation.
- `bash scripts/android/validate_agent_apis.sh` validates Android agent docs, optionally against the SDK source when `SDK_DIR` is set.
- `bash scripts/android/check_api_coverage.sh` generates coverage reports for agent instructions against the CloudX Android API.
- `bash scripts/flutter/validate_agent_apis.sh` performs the equivalent checks for Flutter prompts.

## Coding Style & Naming Conventions
- Author agent files in Markdown with concise, directive sections; prefer fenced code blocks with language hints (` ```kotlin`, ` ```dart`, ` ```yaml`, ` ```bash`).
- Keep prompt headings in Title Case, step labels in imperative voice, and reuse existing snippet formatting.
- Shell scripts follow bash strict mode (`set -euo pipefail` where feasible) and 4-space indentation for continued blocks.

## Testing Guidelines
- Run the relevant `validate_agent_apis.sh` script before opening a PR; rerun after changing SDK references or prompts.
- Provide an SDK checkout via `export SDK_DIR=/path/to/...` when verifying new API tokens or class names.
- Cross-check docs in `docs/<platform>/` to ensure new agent behaviors are reflected in publisher guides.

## Commit & Pull Request Guidelines
- Use short, imperative subject lines (`"Update Android integrator fallback"`), mirroring existing Git history.
- Reference issues with `Fixes #123` or `Refs #123` in the body and summarise scenario coverage plus validation commands.
- PR descriptions should list affected agents/platforms, attach screenshots or logs when CLI output changed, and note SDK version updates.

## Agent-Specific Notes
- When introducing a new role, duplicate the platform directory structure, update install arrays, and add validation coverage so the automation scripts stay aware of the agent.
