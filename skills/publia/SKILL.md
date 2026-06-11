---
name: publia
description: Upload webpages, Markdown files, and images to Publia through the `publia` CLI and return public preview URLs. Use when an agent needs to publish generated files for preview or sharing.
---

# Publia Skill

Use Publia when you need to upload files for preview or sharing.

## Requirements

- Use the `publia` CLI as the only upload mechanism.
- Prefer non-interactive auth with `PUBLIA_TOKEN` when available.
- Default server is `https://getpublia.com`.
- Do not upload sensitive information.

## Verify Access

Before uploading, verify the CLI can reach Publia:

```bash
publia auth whoami --json
```

If this fails because the user is not logged in, ask the user to run:

```bash
publia auth login
```

In a non-interactive environment, ask the user for a Publia CLI token and set:

```bash
export PUBLIA_TOKEN=publia_xxx
```

The default server is already `https://getpublia.com`. Set `PUBLIA_URL` only when the user explicitly wants a different Publia server.

## Upload Files

Upload a file or directory:

```bash
publia upload <file-or-dir> --json
```

Upload into a specific remote directory:

```bash
publia upload <file-or-dir> --to /agent-output --json
```

Read the returned JSON:

- `data.entryUrl` is the best URL to show for a webpage directory.
- `data.uploaded[].url` contains individual file URLs.
- `summary` is a short human-readable result.
- `breadcrumbs` contains useful follow-up commands.

## Navigation

List files:

```bash
publia ls [path] --json
```

Change the saved remote directory:

```bash
publia cd <path>
```

Open a file or directory:

```bash
publia open <path> --json
```

For a file, `open` returns the public URL. For a directory, it returns a directory listing.

## Conflict Handling

Uploads fail on conflicts by default.

Use `--rename` only when creating a distinct copy is acceptable:

```bash
publia upload <path> --rename --json
```

Use `--overwrite` only when the user explicitly wants to replace existing files:

```bash
publia upload <path> --overwrite --json
```

## Failure Handling

If `publia` exits non-zero, read stderr and report the failure. Do not assume the file was uploaded.
