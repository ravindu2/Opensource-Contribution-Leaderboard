# Opensource Contribution Leaderboard

A real-time leaderboard that tracks open source contributions (PRs and issues) across your GitHub organization. Built with Node.js, Express, and vanilla frontend. 

![](./docs/images/demo.png)

## What It Does

- Tracks open PRs, merged PRs, and issues per contributor across all repos in a GitHub org
- Updates automatically in the background — no manual refresh needed
- Contributors can be added before they've made their first contribution
- Built-in admin panel for managing the contributor list
- REST API for programmatic access to leaderboard data

## Get It Running (5 minutes)

You need: **Node.js** (v12+) and a **GitHub personal access token**.

```bash
# 1. Clone and enter the project
git clone https://github.com/RocketChat/Opensource-Contribution-Leaderboard.git
cd Opensource-Contribution-Leaderboard

# 2. Create your config
cp src/server/config-example.json src/server/config.json
```

Edit `src/server/config.json`:

```json
{
  "organization": "YourOrg",
  "organizationHomepage": "https://yourorg.com/",
  "organizationGithubUrl": "https://github.com/YourOrg",
  "authToken": "ghp_YOUR_GITHUB_TOKEN",
  "adminPassword": "pick-something-better",
  "delay": "10",
  "serverPort": "62050",
  "contributors": ["contributor1", "contributor2", "contributor3"]
}
```

Then:

```bash
# 3. Install everything
npm run add

# 4. Build and run
NODE_OPTIONS=--openssl-legacy-provider npm run build
cd dist/server
npm install pm2 -g
pm2 start app.js --name "leaderboard"
```

Open **http://localhost:8080** — you're done.

## Config Reference

| Key | What it is |
|---|---|
| `organization` | Your GitHub org name |
| `authToken` | GitHub personal access token (repo read access) |
| `adminPassword` | Password for the admin panel |
| `delay` | Seconds between API calls per contributor (respect rate limits) |
| `serverPort` | Internal backend port (default 62050) |
| `contributors` | Array of GitHub usernames to track |

## Local Development

Two terminals:

```bash
# Terminal 1 — frontend with hot reload
NODE_OPTIONS=--openssl-legacy-provider npm start

# Terminal 2 — backend server
npm run serve
```

Frontend at http://localhost:8080. Backend data won't refresh without terminal 2.

To work on the admin panel, add a third terminal:

```bash
cd admin && npm start
```

## REST API

Endpoints for programmatic access:

- `GET /api/stats` — total contributors, open PRs, merged PRs, issues
- `GET /api/rank` — ranked contributor list (sortable by `mergedprs`, `openprs`, `issues`)
- `GET /api/contributor` — contributor details by username or rank

Full documentation in [REST-API.md](REST-API.md).

## How It Works Under the Hood

1. `app.js` starts Express on port 8080 and spawns `refresh.js` as a background process
2. `refresh.js` loops through your `contributors` list, calling the GitHub API for each
3. Results are written to `src/assets/data/data.json` — no database needed
4. The frontend and REST API both read from `data.json`, so they're always in sync
5. Real-time updates are pushed to connected clients via socket.io

## AI Agents & Vibe Coding

This project is set up for AI-assisted development. See:

- [AGENTS.md](AGENTS.md) — context for any AI coding agent (Copilot, Cursor, Windsurf, Cline, etc.)
- [GEMINI.md](GEMINI.md) — context for Google Gemini

These files give AI agents the full project layout, design decisions, and ground rules so they can help without breaking things.

## License
This project is open source under the Licence [MIT](./LICENSE).
