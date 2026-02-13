# claw.py

A small, self-contained utility for creating and managing `.claw` files — the **OpenClaw Context Archive** format.

## What is a .claw file?

A `.claw` file is a portable, inspectable zip archive that stores agent session context. It is designed for:

- **Session continuation** — Let an AI agent pick up where it left off.
- **Context portability** — Move cognitive working state between containers, pods, or machines.
- **Transparency** — Anyone can unzip and read the contents. No binary blobs.

A `.claw` file is **not** a database dump, not a container snapshot, and not an executable. It is a structured data archive with standardized metadata.

## What's inside a .claw file?

```
session.claw
├── manifest.json    ← Auto-generated metadata (format version, timestamp, file list)
├── README.md        ← Auto-generated human-readable explanation
├── claw.py          ← Copy of this script (self-contained portability)
└── context/         ← Agent working context files
```

Every `.claw` archive includes its own copy of `claw.py` so that the archive is fully self-describing and anyone who receives it can re-export without external tooling.

## Usage

### Export context to a .claw file

```bash
python claw.py export /app/context session.claw
```

Packages the directory into a `.claw` archive with fresh `manifest.json` and `README.md`.

### Import a .claw file

```bash
python claw.py import session.claw /app/context
```

Extracts the archive into the target directory.

### Inspect a .claw file

```bash
python claw.py info session.claw
```

Prints metadata and file listing without extracting.

## Design principles

- **Just a zip.** Rename to `.zip` and open with any archive tool.
- **Declarative, not executable.** The archive describes state; the runtime decides behavior.
- **Metadata consistency.** `manifest.json` and `README.md` are always regenerated on export — never stale.
- **No secrets.** Context archives should never contain credentials, tokens, or database connection strings.
- **Versioned from day one.** The `manifest.json` includes a format version for forward compatibility.

## Intended use with Docker/Kubernetes

In containerized environments, `.claw` files provide clean agent session continuity:

1. Agent pod mounts `/context` volume
2. Import: `python claw.py import session.claw /context`
3. Agent runs, accumulates working state
4. Export: `python claw.py export /context session.claw`
5. Archive saved to mounted drive or object storage
6. New pod imports and continues

The container stays stateless. Cognition transfers via `.claw`.

## Format specification

**OpenClaw Context Archive Specification v0.1**

| Field | Description |
|-------|-------------|
| `format` | Always `"openclaw-context"` |
| `version` | Spec version (currently `"0.1"`) |
| `created_at` | ISO 8601 UTC timestamp |
| `generated_by` | Always `"claw.py"` |
| `context_files` | List of relative paths under `context/` |

## Dependencies

Python 3.6+ (standard library only — no pip packages required).
