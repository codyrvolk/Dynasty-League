# Dynasty League

Tools for managing a Sleeper dynasty fantasy football league with Claude Code, via an MCP server that talks to the [Sleeper API](https://docs.sleeper.app/).

## What's here

- `vendor/sleeper-api-mcp` — [anthonybaldwin/sleeper-api-mcp](https://github.com/anthonybaldwin/sleeper-api-mcp), vendored as a git submodule. An MCP server exposing Sleeper league/roster/matchup data plus trade analysis, waiver recommendations, and lineup optimization tools.
- `.mcp.json` — registers that server with Claude Code under the name `sleeper`.
- `.claude/agents/dynasty-manager.md` — a subagent tuned for dynasty-league questions (trades, waivers, lineups, rookie drafts) that uses the `sleeper` MCP tools instead of guessing at rosters or stats.
- `.env.example` — template for the Sleeper username/league ID env vars the MCP server reads.

## Setup

1. **Clone with submodules** (or run `git submodule update --init` if you already cloned):
   ```bash
   git clone --recurse-submodules <this repo>
   ```

2. **Install the MCP server's dependencies** (requires [Bun](https://bun.sh)):
   ```bash
   cd vendor/sleeper-api-mcp && bun install && cd ../..
   ```

3. **Find your Sleeper username and league ID.** The league ID is the number in a league's URL on sleeper.app (e.g. `sleeper.app/leagues/<league_id>/...`). If you only know your username, ask the agent — `get_user_leagues` will list your leagues once a username is configured.

4. **Configure credentials.** Copy `.env.example` to `.env` and fill in your values, then export them before starting Claude Code so `.mcp.json`'s `${VAR}` expansion can see them:
   ```bash
   cp .env.example .env
   # edit .env with your username/league ID
   export $(grep -v '^#' .env | xargs)
   ```
   To follow a second Sleeper account or league, uncomment and fill in the `_B` variables (see `.env.example`); the server disambiguates which league you mean from context.

5. **Start Claude Code** in this repo. It should prompt to approve the project-scoped `sleeper` MCP server on first use — approve it, then ask the `dynasty-manager` subagent things like:
   - "What's my optimal lineup this week?"
   - "Analyze this trade: I give up [player] for [player] + a 2027 1st"
   - "Who are the best waiver adds at RB right now?"
   - "Preview my week 10 matchup"

## Updating the MCP server

The server is a submodule pinned to a specific upstream commit. To pull in upstream changes:
```bash
cd vendor/sleeper-api-mcp && git pull origin main && bun install && cd ../..
git add vendor/sleeper-api-mcp
git commit -m "Update sleeper-api-mcp submodule"
```
