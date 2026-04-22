---
description: Preview the status line by piping sample JSON into ~/.claude/scripts/statusline.sh (supports --no-quotes). Shows energy/water estimates.
allowed-tools: [Bash(test:*), Bash(echo:*), Bash(pwd:*), Bash(cat:*)]
---

# Preview Statusline

Render a sample status line using the installed script and mock JSON. Useful to verify colors, layout, and energy/water estimates.

## Steps

1) Ensure the script is installed:

- Run: `test -x ~/.claude/scripts/statusline.sh || (echo "Not installed. Run: /statusline.install-statusline" && exit 1)`

2) Build a sample event JSON (30-min Sonnet session) and pipe to the script:

- Run:

```
cat <<'JSON' | ~/.claude/scripts/statusline.sh
{
  "cost": {
    "total_cost_usd": 0.0123,
    "total_duration_ms": 1845000,
    "total_lines_added": 10,
    "total_lines_removed": 2
  },
  "model": {
    "display_name": "Sonnet 4.5",
    "id": "claude-sonnet-4-5-20250929"
  },
  "workspace": {
    "project_dir": "$(pwd)"
  }
}
JSON
```

You should see a single status line with colored fields, energy/water estimates (⚡ and 💧), and a quote.

3) Preview with a different model tier (2-hour Opus session):

- Run:

```
cat <<'JSON' | ~/.claude/scripts/statusline.sh --no-quotes
{
  "cost": {
    "total_cost_usd": 1.2345,
    "total_duration_ms": 7200000,
    "total_lines_added": 500,
    "total_lines_removed": 100
  },
  "model": {
    "display_name": "Opus 4",
    "id": "claude-opus-4-20250929"
  },
  "workspace": {
    "project_dir": "$(pwd)"
  }
}
JSON
```

This shows higher wattage and water consumption in gallons for a longer Opus session.

To preview without the quote section, either add the flag or prefix an env var:

```bash
# Using flag
cat <<'JSON' | ~/.claude/scripts/statusline.sh --no-quotes
{ "cost": {"total_cost_usd": 0.0123, "total_duration_ms": 1845000, "total_lines_added": 10, "total_lines_removed": 2 }, "model": {"display_name": "Sonnet 4.5", "id": "claude-sonnet-4-5-20250929"}, "workspace": {"project_dir": "$(pwd)"} }
JSON

# Using environment variable
CLAUDE_STATUSLINE_NO_QUOTES=1 bash -lc 'cat <<'\''JSON'\'' | ~/.claude/scripts/statusline.sh'
{ "cost": {"total_cost_usd": 0.0123, "total_duration_ms": 1845000, "total_lines_added": 10, "total_lines_removed": 2 }, "model": {"display_name": "Sonnet 4.5", "id": "claude-sonnet-4-5-20250929"}, "workspace": {"project_dir": "$(pwd)"} }
JSON
```
