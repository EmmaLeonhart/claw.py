# claw.py

## Project Description
`claw.py` is a small self-contained Python utility for creating and managing `.claw` files — the OpenClaw Context Archive format. A `.claw` file is a standardized zip archive that stores agent session context for portability and continuation.

## Architecture and Conventions

### Core design decisions
- **Just a zip file.** `.claw` is a zip with a custom extension. Nothing proprietary.
- **Self-contained.** Every `.claw` archive includes a copy of `claw.py` itself, so the archive is portable and self-describing without external dependencies.
- **Metadata is always regenerated.** `manifest.json` and `README.md` are freshly generated on every export — existing ones in the source directory are ignored (not nested ones in context/).
- **Standard library only.** No pip dependencies. Works on any Python 3.6+.
- **Declarative, not executable.** The archive contains data and instructions, not runnable code. The runtime decides behavior.

### File structure
```
claw.py           ← The utility script (single file, ~200 lines)
README.md         ← Human-facing docs
CLAUDE.md         ← This file
```

### .claw archive internal structure
```
archive.claw
├── manifest.json    ← Auto-generated metadata
├── README.md        ← Auto-generated human explanation
├── claw.py          ← Copy of the builder script
└── context/         ← Agent context files
```

### Three-layer separation (from architectural discussion)
1. **Database layer** — Authoritative graph/data. Long-term. NOT in the .claw file.
2. **Agent context layer** — Cognitive working state. Semi-persistent. THIS is what .claw stores.
3. **Container layer** — Stateless execution environment (Ubuntu/Docker). Reproducible.

### CLI commands
- `python claw.py export <dir> [output.claw]` — Package directory into archive
- `python claw.py import <file.claw> [target_dir]` — Extract archive
- `python claw.py info <file.claw>` — Inspect without extracting

## Workflow Guidelines
- **Commit early and often.** Every meaningful change should be committed with a clear, descriptive summary.
- **Keep this CLAUDE.md up to date.** Document decisions as they happen.
- **Use `python` not `python3`** on this Windows system.

## Long command series run in strict order
When Emma gives a long series of commands, treat it as a long series of commands to be
executed in relatively STRICT ORDER, one after another, EVEN IF the order seems not to
make sense or seems inefficient. The sequencing is intentional — she organizes the steps
so states change in the order she wants. Do not reorder, merge, or skip steps.
