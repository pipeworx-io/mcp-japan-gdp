# @pipeworx/japan-gdp

Japan's quarterly GDP estimates from the Cabinet Office, Government of Japan (expenditure approach): growth quarter-on-quarter and annualized, year-on-year, levels and contributions, from January-March 1994, tagged with the release date and whether the estimate is the first or second preliminary.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 2014+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `jp_gdp_latest(release_date?)` — the newest release (or one chosen by date): quarter covered, stage, release date, and real and nominal GDP growth quarter-on-quarter and annualized with components (private consumption, private non-residential investment, exports, imports and more), plus real contributions to annualized growth.
- `jp_gdp_series(measure?, basis?, adjustment?, components?, from?, to?, release_date?)` — dated quarterly rows by component. `measure` real or nominal; `basis` qoq, annualized, yoy, amount, contribution_qoq, contribution_annualized, contribution_yoy; default the last 8 quarters.
- `jp_gdp_releases(year?)` — the releases of a year, newest first, with the stage of each.

## Auth

Keyless.

## Data sources

- Cabinet Office, Government of Japan, Economic and Social Research Institute: Quarterly Estimates of GDP. Fetched live on every call; the current release is found from the publisher's release listing, so a new quarter appears when the publisher releases it. Every response carries `data_as_of` with the quarter, the estimate stage and the release date.

## Honest limits

A preliminary estimate is revised: a second preliminary estimate follows about three weeks after the first, and the publisher re-estimates every seasonally adjusted figure back to 1994 at each release. Expenditure-approach GDP only: no industry breakdown, deflators, fiscal-year or calendar-year tables, or household consumption by type. Values printed as *** by the publisher are returned as null. A request with no matching quarter or release returns `no_match: true`; a source that cannot be read is an error.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "japan-gdp": {
      "url": "https://gateway.pipeworx.io/japan-gdp/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/japan-gdp/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 2014+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/jp_gdp_latest \
  -H 'Content-Type: application/json' \
  -d '{}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/jp_gdp_latest`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-japan-gdp` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/japan-gdp/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "japan-gdp": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-japan-gdp"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Japan Gdp data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
