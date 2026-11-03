# setup – Agent Context

Implementation guide for coding agents changing **setup**. User-facing run instructions: [`README.md`](README.md).

---

## Orientation

| Question | Answer |
| -------- | ------ |
| What is this? | Personal **machine bootstrap** scripts (Linux + Windows) for essential developer tools |
| Package? | **No** — not a Python package; no `pyproject.toml` / tests tree |
| Entry points | `linux.py` (primary Linux), `linux.sh` (legacy bash), `windows.ps1` (Windows), `windows.py` (empty stub) |
| Runtime | System Python 3 for `linux.py`; bash; PowerShell + Chocolatey on Windows |
| Tests | None today — **do not invent a delete policy** if a `tests/` tree appears later |
| Secrets | Never commit Modular auth tokens, API keys, or installer credentials — use env vars |

```text
.
├── linux.py       # Primary Linux installer (Executable / Installable model)
├── linux.sh       # Legacy apt + Modular bash installer
├── windows.ps1    # Chocolatey + Poetry Windows bootstrap
├── windows.py     # Empty stub — do not pretend it runs
├── README.md      # User docs
└── AGENTS.md      # This file
```

Thin GitHub Actions CI/semver (when present under `.github/`) should pin [`danielnachumdev/actions`](https://github.com/danielnachumdev/actions) (`@latest` or `@v1`). How to adopt: [`HOW-TO-USE.md`](https://github.com/danielnachumdev/actions/blob/v1/HOW-TO-USE.md). Do not re-vendor shared workflow bodies here.

---

## Develop / change scripts

There is no `uv sync` or installable package. Edit the script for the platform you care about and run it deliberately.

| Task | Command / note |
| ---- | -------------- |
| Linux dry-run | `python3 linux.py` with `DEBUG = True` (default) — explains steps; `TerminalCommand.execute` returns early |
| Linux real install | Set `DEBUG = False`, then `python3 linux.py` (needs `sudo`) |
| Linux legacy | `bash linux.sh` (apt + Modular; review before run) |
| Windows | Admin PowerShell → `.\windows.ps1` |
| Windows Python | `windows.py` is empty — extend or leave stub; do not document as working |

### Domain notes (`linux.py`)

- **`DEBUG`:** module-level flag. When `True`, `TerminalCommand.execute` no-ops after explain; `FileAppender` only logs. Flip to `False` only for intentional installs.
- **Model:** `Executable` / `Installable` / `AptTarget` / `InstallTarget` / `TerminalCommand` / `FileAppender` — extend `EXECUTABLES` rather than scattering one-off `subprocess` calls in `main`.
- **`cmrt`:** streams combined stdout/stderr from shell commands; used by `TerminalCommand` and sudo elevation probe.
- **OS guard:** `main` exits unless `get_os() == OSType.LINUX`.
- **Modular / Mojo:** install curl must not embed auth tokens in git. Read `MODULAR_AUTH` (or similar) from the environment; never paste live tokens into scripts or docs.
- **Anaconda:** pinned installer URL in `EXECUTABLES` — bump deliberately when updating.

### Windows (`windows.ps1`)

- Installs Chocolatey if needed, then packages (`git`, `anaconda3`, `mingw`, `make`, `rabbitmq`, `redis-64`, `docker-desktop`) and Poetry via the official installer URL.
- Typo risk: `mingq` in the script may be intended as `mingw` — verify before “fixing” silently if changing package names.
- Keep secrets out of the script body.

---

## PRs, CI, and releases

1. Branch off `main`; open a PR into `main`.
2. Attach **exactly one** semver label: `noBump` | `bumpPatch` | `bumpMinor` | `bumpMajor`.
3. Docs-only / workflow-only / Dependabot → **`noBump`**.
4. Prefer required checks **`CI / CI`** and **`Semver / Semver`** when those workflows exist.
5. Change shared CI/semver behavior in **`danielnachumdev/actions`**, not by copying YAML bodies into this repo.

There is no PyPI publish path — this is not a distributable package.

---

## Sensitive / ignored

Do not commit: Modular / vendor auth tokens, `.env`, API keys, local installer caches, or secrets in script literals. Prefer env injection for anything credential-like. Respect `.gitignore`.
