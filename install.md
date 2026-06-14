# Install Publia Skills

Publia Skills is an instruction file for AI assistants. Once installed, your
assistant knows how to drive the `publia` CLI — uploading webpages, Markdown, and
images to Publia and handing back a public preview link.

## 1. Install the Publia CLI

Check whether `publia` is already available:

```bash
publia --help
```

If it isn't installed, install it from npm:

```bash
npm install -g publia-cli
```

Confirm it works:

```bash
publia --help
publia commands --json
```

## 2. Sign in to Publia

For normal use, sign in through the browser:

```bash
publia auth login
publia auth whoami --json
```

The default server is already `https://publia.ai`, so you normally don't need to
set `PUBLIA_URL`.

For environments without a browser (CI, remote shells), issue a token from the
Publia web app under **Settings → Developer**, then set it as an environment
variable:

```bash
export PUBLIA_TOKEN=publia_xxx
publia auth whoami --json
```

Each account keeps only one CLI token. Issuing a new token immediately
invalidates the previous one, so prefer `publia auth login` for everyday use.

## 3. Install the Skill

Install with the skills installer:

```bash
npx skills add getpublia/skills
```

Target a specific tool if needed:

```bash
npx skills add getpublia/skills -a codex
npx skills add getpublia/skills -a claude-code
```

Or install it globally:

```bash
npx skills add getpublia/skills -g
```

Verify it's listed:

```bash
npx skills list
# you should see publia
```

Restart your assistant session afterward so it picks up the new skill.

## 4. Verify uploading

Test with a small directory:

```bash
publia upload ./output-site --to /agent-output --json
publia open /agent-output/output-site --json
```

On success, the response includes a public URL you can open.

## Manual install

If the skills installer isn't available, install by hand:

```bash
git clone https://github.com/getpublia/skills ~/.publia-skills
mkdir -p ~/.codex/skills
ln -sfn ~/.publia-skills/skills/publia ~/.codex/skills/publia
```

To update, pull the latest:

```bash
cd ~/.publia-skills && git pull
```
