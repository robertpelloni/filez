# cli/ — fo_cli Executable

> Parent: [../AGENTS.md](../AGENTS.md)

## Overview

Command-line interface with 15+ commands. Entry point: `fo_cli.cpp`. Commands in `cmd_*.cpp` files.

## Structure

```
cli/
├── fo_cli.cpp      # Main entry, argument parsing, command dispatch
├── cmd_scan.cpp    # scan command
├── cmd_duplicates.cpp
├── cmd_hash.cpp
├── cmd_metadata.cpp
├── cmd_ocr.cpp
├── cmd_similar.cpp
├── cmd_classify.cpp
├── cmd_organize.cpp
├── cmd_delete.cpp
├── cmd_rename.cpp
├── cmd_export.cpp
├── cmd_undo.cpp
├── cmd_history.cpp
└── cmd_lint.cpp    # Filesystem linter (empty files, temp files)
```

## Adding a New Command

1. Create `cmd_yourcommand.cpp`
2. Implement command function:
```cpp
int cmd_yourcommand(int argc, char* argv[]) {
    // Parse args, call Engine methods, output results
    return 0;  // Success
}
```
3. Add to dispatch table in `fo_cli.cpp`:
```cpp
{"yourcommand", cmd_yourcommand},
```
4. Update `print_usage()` with command description
5. Add test in `../tests/` if applicable

## Command Reference

| Command | Description | Key Options |
|---------|-------------|-------------|
| `scan` | Scan directories | `--scanner=`, `--ext=`, `--recursive` |
| `duplicates` | Find duplicates | `--hasher=`, `--min-size=` |
| `hash` | Compute hashes | `--hasher=`, `--format=` |
| `metadata` | Extract EXIF | `--provider=` |
| `ocr` | Extract text | `--provider=`, `--lang=` |
| `similar` | Perceptual hash search | `--phash=`, `--threshold=` |
| `classify` | AI classification | `--model=` |
| `organize` | Rule-based move | `--rules=`, `--dry-run` |
| `export` | Export results | `--format=json|csv|html`, `--output=` |
| `lint` | Find filesystem issues | `--empty`, `--temp`, `--broken` |

## Output Formats

All commands support `--format=json` for machine-readable output. Default is human-readable text.

## Conventions

- Exit 0 on success, non-zero on error
- Use `std::cerr` for errors, `std::cout` for output
- Support `--help` for each command
- Use Engine methods, don't duplicate core logic
