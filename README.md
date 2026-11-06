# MCP Server Change and Health Monitor MCP Server

[![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-server-change-health-monitor)](https://www.npmjs.com/package/@mambalabsdev/mcp-server-change-health-monitor) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-server-change-health-monitor)](https://github.com/mambalabsdev/mcp-server-change-health-monitor/blob/main/LICENSE)

An MCP server for the Mamba Labs MCP Server Change and Health Monitor actor on Apify. Check any list of MCP servers and get one row per server: did it answer, what it exposes, and what changed.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

Give it MCP server URLs, npm packages, or a search of the official MCP Registry. It speaks the MCP protocol to each server and returns whether the server answered, how fast, the tools it exposes now, and what changed since the last run. Changes are marked breaking, warning, or info.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-mcp-server-change-health-monitor": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-server-change-health-monitor"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `check_mcp_servers` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "Are these three MCP servers up, and did any of their tools change since last week?"
- "Check every server in the official MCP Registry that matches github and list which ones answer."
- "Did the tool descriptions on this MCP server change since my last check?"

## Tool

`check_mcp_servers`. One tool. Example arguments:

```json
{
  "server_urls": [
    "https://mcp.deepwiki.com/mcp"
  ]
}
```

## Inputs

Every buyer facing input of the actor. Omitted inputs take the actor's own defaults.

| Input | Type | Required | Description |
|---|---|---|---|
| `server_urls` | array | no | HTTP endpoints of MCP servers. Streamable HTTP is tried first, then the older HTTP plus SSE transport. |
| `npm_packages` | array | no | npm package names, optionally with a version, such as @modelcontextprotocol/server-everything. Each is installed and started inside your run. The code in a package you list runs in your run, so list only packages you trust. No environment variables are passed to it. |
| `registry_search` | array | no | Search terms for the official MCP Registry. The HTTP endpoints of matching servers are checked. Packages found this way are listed but never run, because running code picked by a keyword is not safe. |
| `max_servers` | number | no | Most servers to check per registry search term, up to 200. Default 25. |
| `snapshot_key` | string | no | Name of the memory that holds the last run's results, so a scheduled run finds changes on its own. Use a different name per list. Default default. |
| `previous_snapshot` | string | no | Optional. The JSON of a SNAPSHOT record from an earlier run, or an https link to that record. It takes the place of the saved snapshot for this run. |
| `timeout_seconds` | number | no | How long one request to a server may take before it counts as no answer, 3 to 60. Default 20. A package gets 60 more seconds to install. |
| `include_resources_prompts` | boolean | no | Also list the server's resources and prompts and report what changed in them. Default true. |
| `auth_headers` | string | no | Optional, for servers you are allowed to check. JSON keyed by site, for example {"https://mcp.example.com":{"Authorization":"Bearer abc"}}. Each header goes only to its own site. Encrypted by Apify, never logged, never saved in a snapshot. |
| `max_charge_usd` | string | no | Optional. The run stops before a charge that would pass this amount. Free rows are always returned. |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `server-check`, one MCP server checked | $0.005 | $0.00475 | $0.0045 | $0.00425 |
| `package-check`, one npm package installed and checked | $0.02 | $0.019 | $0.018 | $0.017 |
| `apify-actor-start`, per run start | $0.002 | $0.002 | $0.002 | $0.002 |

Servers that need credentials or refused the request, invalid entries, registry rows, and refused input are free. Mamba Labs pays the platform costs of the run, Apify proxy traffic included.

## Full actor documentation

[apify.com/mambalabs/mcp-server-change-health-monitor](https://apify.com/mambalabs/mcp-server-change-health-monitor)

## License

MIT. See [LICENSE](LICENSE).
