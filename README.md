# setup

**Personal machine bootstrap scripts** — install the essentials on a fresh Linux or Windows box (build tools, git, Redis, RabbitMQ, Anaconda, Modular/Mojo, Chocolatey packages, and friends).

This is a **personal** toolkit, not a published package. Scripts live at the repo root and talk to package managers (`apt`, Chocolatey, installers) with elevated privileges.

## Quick start

### Linux (preferred)

```bash
# Dry-run by default: DEBUG=True in linux.py skips real command execution
python3 linux.py
```

Set `DEBUG = False` near the top of `linux.py` when you intend to install for real. Expect `sudo` prompts.

Legacy shell path (apt packages + Modular install):

```bash
bash linux.sh
```

### Windows

Run PowerShell **as Administrator**:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
.\windows.ps1
```

`windows.py` is an empty stub today — use `windows.ps1`.

## What gets installed

| Platform | Script | Rough contents |
| -------- | ------ | -------------- |
| Linux | `linux.py` | `apt` update/upgrade; build-essential, git, gcc/g++, cmake, make; Redis; Modular + Mojo (+ `~/.bashrc` PATH); RabbitMQ; Anaconda3 installer |
| Linux | `linux.sh` | Smaller apt set + Modular curl installer |
| Windows | `windows.ps1` | Chocolatey → git, Anaconda3, mingw, make, RabbitMQ, Redis, Docker Desktop; Poetry installer |

Exact package lists live in the scripts — edit those files to customize.

## Secrets and safety

- **Never commit** API tokens, Modular auth values, or other credentials. Prefer environment variables (e.g. `MODULAR_AUTH`) over hardcoding.
- Scripts request **admin/`sudo`**. Review every command before flipping `DEBUG` off.
- Anaconda and similar installers are large and interactive — plan for time and prompts.

## Docs

| Doc | Audience |
| --- | -------- |
| [`AGENTS.md`](AGENTS.md) | Coding agents — layout, `DEBUG` behavior, PR labels |
| This README | Humans running the scripts |
