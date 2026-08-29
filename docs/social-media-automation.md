# Social media automation with Claude Code

This repo is now configured so that Claude Code automatically knows about
the **postiz** plugin from Anthropic's official plugin marketplace
(`anthropics/claude-plugins-official`). That plugin wraps the
[Postiz](https://postiz.com) CLI — a tool for scheduling and publishing
posts across 28+ social platforms (X/Twitter, LinkedIn, Instagram, Facebook,
TikTok, YouTube, Reddit, Pinterest, Discord, Slack, and more) from the
command line, and therefore from Claude Code.

Postiz does **not** talk to social platforms directly on its own — it's a
scheduling/automation layer that sits in front of the accounts you connect
to it. The one-time setup below (creating an account and connecting your
platforms) has to be done by a Foundation team member, since it requires
logging into your own social accounts.

## What was set up in this repo

- `.claude/settings.json` — declares the `claude-plugins-official`
  marketplace and enables the `postiz` plugin, so anyone opening this repo
  in Claude Code gets it automatically (no manual `/plugin install`
  needed).
- `.env.example` — template for the `POSTIZ_API_KEY` you'll create below.
  Copy it to `.env` and fill in the real key; `.env` is gitignored and
  must never be committed.

## One-time setup (do this once, outside Claude Code)

1. **Create a Postiz account** at [postiz.com](https://postiz.com) (or
   stand up a self-hosted instance — see
   [Postiz's self-hosting docs](https://docs.postiz.com) — if the
   Foundation prefers not to use their cloud).
2. **Connect your social accounts** in the Postiz dashboard (Settings →
   Integrations): X, LinkedIn, Instagram, Facebook, etc. Each platform
   goes through its own OAuth consent screen — only someone with admin
   access to the Foundation's social accounts can do this step.
3. **Get an API key**: Postiz dashboard → Settings → API Keys → generate
   one.
4. Put it in your local environment (never in a committed file):
   ```bash
   cp .env.example .env
   # edit .env and set POSTIZ_API_KEY=...
   ```
   or export it directly in your shell profile:
   ```bash
   export POSTIZ_API_KEY=your_api_key_here
   ```

Alternatively, skip the API key and authenticate interactively:
```bash
npx postiz auth:login   # OAuth2 device-flow login, no key needed
```

## Using it from Claude Code

Once `.claude/settings.json` has loaded (just open this repo in Claude
Code), the `postiz` skill is available and Claude can run the CLI on your
behalf. A few examples you can ask for directly in chat, or run yourself:

**See what's connected:**
```bash
npx postiz integrations:list
```

**Post an announcement to multiple platforms at once:**
```bash
npx postiz posts:create \
  -c "The Bambo Foundation is building trust in digital technology across Africa. Learn more at bambofoundation.africa" \
  -s "2026-09-01T09:00:00Z" \
  -i "twitter-id,linkedin-id,facebook-id"
```
(Replace the IDs with the ones from `integrations:list`.)

**Check performance:**
```bash
npx postiz analytics:platform <integration-id> -d 30
```

Ask Claude things like *"schedule this update across our X and LinkedIn
for Monday at 9am"* or *"pull last month's engagement numbers for our
Instagram"* — with the plugin enabled and integrations connected, Claude
can drive these commands directly.

## Security notes

- Never commit `POSTIZ_API_KEY` or any social platform OAuth token to this
  repository. `.env` is gitignored for this reason.
- The plugin only automates *scheduling/posting/reading analytics* through
  your connected Postiz account — it cannot do anything on a platform you
  haven't explicitly connected in the Postiz dashboard.
- Review scheduled/draft posts (`npx postiz posts:list`) before they go
  live if you want a human-in-the-loop check.

## Reference

- Plugin/marketplace entry: `anthropics/claude-plugins-official`,
  plugin name `postiz`
- Underlying CLI source: <https://github.com/gitroomhq/postiz-agent>
- Full command reference: see that repo's `README.md` and `SKILL.md`
- Postiz product docs: <https://docs.postiz.com>
