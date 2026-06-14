---
name: publia
description: Publish webpages, Markdown, and images to the web via the `publia` CLI and hand back a live shareable URL. Use whenever you produce a file the user will want to view in a browser or share — a generated page, report, slide, image, or static site. You run the whole flow yourself and return the link; the user never has to think about uploading.
---

# Publia

Publia turns a local file or folder into a public, shareable URL. You (the agent)
own the entire publish flow. The user should only ever see the final link — not
the CLI, not the auth, not the mechanics.

**Default server: `https://publia.ai`.** It is already the CLI default; only set
`PUBLIA_URL` if the user explicitly names a different Publia server.

## The golden path

When the user wants something they made put online ("publish this", "share this
page", "give me a link", "host this"), run this loop yourself, silently:

1. **Make sure the CLI is reachable** — see *Setup*.
2. **Make sure you're signed in** — `publia auth whoami --json`. If it fails, do *Auth*.
3. **Upload** — `publia upload <file-or-dir> --json`.
4. **Hand back the URL** — read `data.entryUrl` (for a site/page) or
   `data.uploaded[].url`, and give the user just that link.

Do not narrate steps 1–3 or paste raw CLI output. The user asked for a link;
give them the link.

## Setup

Check once:

```bash
publia --help
```

If `publia` is not found, install it (this is yours to do, not the user's):

```bash
npm install -g publia-cli
```

## Auth

Authentication persists in `~/.config/publia/config.json`, so this is normally a
one-time event. Check first:

```bash
publia auth whoami --json
```

If that succeeds, you're done — proceed to upload.

**If it fails, pick the path that fits the environment:**

- **Interactive (a browser is available):** run

  ```bash
  publia auth login
  ```

  This opens the browser, the user signs in, and the terminal is signed in
  automatically when they finish. This is the *only* moment you may interrupt the
  user — tell them plainly: "A browser window opened — sign in to Publia and it'll
  finish on its own." Then re-check `whoami` and continue.

- **Headless / no browser (CI, remote shell, sandbox):** ask the user once for a
  token from Publia (web app → Settings → Developer → issue token), then:

  ```bash
  export PUBLIA_TOKEN=publia_xxx
  ```

  The CLI reads `PUBLIA_TOKEN` and skips login entirely.

**Important — one token per account.** Issuing a token from the web revokes the
account's previous CLI session. So *prefer `publia auth login`* for normal use.
Only have the user issue a token when there is genuinely no browser, and warn
them it will sign out any other CLI they had logged in.

## Uploading

A single file or a whole directory (a static site):

```bash
publia upload ./site --json
publia upload report.html --json
```

Into a specific remote directory:

```bash
publia upload ./site --to /reports --json
```

Reading the result envelope:

- `data.entryUrl` — the best link to show for a webpage/site (resolves to the
  directory's `index.html`). **Prefer this when present.**
- `data.uploaded[].url` — individual public file URLs.
- `data.skipped[]` — files skipped as unsupported or ignored.
- `summary` — short human-readable result.
- `breadcrumbs` — ready-to-run follow-up commands.

## Putting a page on the owner's homepage

Uploaded pages are **link-accessible by default but not listed** on the owner's
public homepage. To feature one on the homepage:

```bash
publia publish <path> --json            # list it on the homepage
publia visibility <path> public --json  # same thing
publia visibility <path> private --json # unlist it again (still link-accessible)
```

Only do this when the user wants the page shown on their public profile; for a
one-off share link, plain `upload` is enough.

## Browsing & managing

```bash
publia ls [path] --json                 # list a remote directory
publia cd <path>                        # set the saved working directory
publia open <path> --json               # file -> public URL; dir -> listing
publia search <query> --limit 20 --json # find files by name
publia usage --json                     # storage used, file count, quota
```

```bash
publia mv <path> [path...] --to <dir> --json   # move
publia rename <path> <new-name> --json          # rename
publia cp <path> --json                         # duplicate
publia mkdir <path> --json                      # create a directory
publia rm <path> [path...] --json               # move to trash
publia trash [list] --json                      # list trash (shows ids)
publia trash restore <id> --json                # restore by id
publia trash empty --yes --json                 # permanently empty trash
```

`publia commands --json` prints the full machine-readable catalog.

## Conflicts

`upload` fails on a name conflict by default. Resolve deliberately:

- `--rename` — keep both; upload as a distinct copy. Use when a fresh copy is fine.
- `--overwrite` — replace the existing file. Use only when the user clearly wants
  to replace the previous version.

Never guess: if replacing vs. duplicating matters and intent is unclear, ask.

## Constraints

- **Supported types only:** Markdown (`.md/.markdown/.mdx`), web (`.html/.css/.js/
  .json/.webmanifest`), fonts (`.woff/.woff2/.ttf/.otf/.eot`), and images (`.png/
  .jpg/.jpeg/.gif/.webp/.svg/.avif/.ico/.bmp`). Other files are silently skipped —
  check `data.skipped`.
- **Limits:** ≤10 MB per file, ≤200 files and ≤200 MB per upload, max directory
  depth 12. Hidden files and symlinks are skipped.
- **Never upload secrets** — API keys, `.env`, tokens, credentials, private data.
  Published files are publicly reachable by URL.

## Failure handling

If any `publia` command exits non-zero, read stderr and report what actually
happened. Never claim a file was published unless the command succeeded and you
have the URL from its output. With `--json`, a failure is still reported on
stderr and via a non-zero exit code.
