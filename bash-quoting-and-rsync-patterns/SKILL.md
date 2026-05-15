---
description: Covers common bash quoting pitfalls and rsync command patterns for transferring files with special characters in names. Use when a user is building rsync commands, escaping file paths, or hitting errors with brackets, spaces, or backslashes in filenames.
metadata:
  origin: agent
---

# Bash Quoting & rsync Patterns

## Core Quoting Rules

### Single Quotes (`'...'`)
- Preserve **everything verbatim** — no escaping happens inside
- Backslashes are **literal characters**, not escape sequences
- Spaces, brackets `[]`, parens `()`, `$`, and `*` are all safe — no quoting needed inside

```bash
# WRONG — backslashes become literal characters in the filename lookup
rsync -av 'Clockwork\ Planet\ \[Blu-ray\]' dest/

# CORRECT — no escaping needed inside single quotes
rsync -av 'Clockwork Planet [Blu-ray]' dest/
```

### Double Quotes (`"..."`)
- Allows `$var`, `` `cmd` ``, and `\` escapes (only before `$`, `` ` ``, `"`, `\`, newline)
- Spaces and brackets are safe; `$` and backticks are still interpreted

### Unquoted / Backslash Escaping
- Each special character must be individually escaped: `Clockwork\ Planet\ \[Blu-ray\]`
- Error-prone for long paths — prefer single quotes instead

---

## rsync Multi-Source Pattern

When transferring multiple directories/files, list each as a separate single-quoted argument:

```bash
rsync -aHAXxv --checksum --info=progress2 --no-compress \
  'Source Dir One' \
  '[Bracketed Dir] With Spaces' \
  'file with (parens) and spaces.mkv' \
  user@host:/mnt/destination/path/
```

Key flags:
| Flag | Purpose |
|---|---|
| `-a` | Archive mode (recursive, preserves permissions, timestamps, symlinks) |
| `-H` | Preserve hard links |
| `-A` | Preserve ACLs |
| `-X` | Preserve extended attributes |
| `-x` | Stay on one filesystem |
| `-v` | Verbose |
| `--checksum` | Compare by checksum, not just size+time |
| `--info=progress2` | Overall progress display |
| `--no-compress` | Skip compression (useful on LAN — saves CPU) |

---

## Common Pitfall: Mixed Escaping

**Symptom:** rsync says "No such file or directory" even though the path looks right.

**Cause:** Backslash-escaped path inside single quotes — the shell sends the literal `\` to the filesystem lookup.

**Fix:** Strip all backslashes when wrapping a path in single quotes.

```bash
# If you have a path like: Clockwork\ Planet\ \[Blu-ray\]
# Convert to:             'Clockwork Planet [Blu-ray]'
```

---

## Debugging Tips

- `echo 'your path here'` — print exactly what the shell sees
- `ls 'path'` — verify the path resolves before passing to rsync
- `rsync -n` (dry-run) — simulate transfer without writing files
