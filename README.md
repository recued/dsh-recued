# dsh-recued

Connects [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness) (`dsh`) to your own
[Recued](https://recued.com) server.

Recued is a server you run on a computer you control. It keeps your notes, tasks, mail, calendar
and the things you own, and it stands between your data and any AI: it checks every request
against what you granted, asks you before a change, and keeps a record of what was done.

This plugin adds one row of DeepSeek Harness's own MCP client (`@deepseek-ai/dsh-mcp-client`),
pointed at your Recued server. It ships no code of its own; the whole plugin is
[`cordis.patch.yml`](cordis.patch.yml).

## What you need

- DeepSeek Harness 0.2 (tested with 0.2.1-rc.2), with `pnpm` on your `PATH` for `dsh plugin`.
- A Recued server: [install it](https://recued.com/docs/setup/install-server/) on a computer you
  control.
- A contract for DeepSeek Harness. In the Recued web app, open Contracts, make a contract, choose
  the tools DeepSeek Harness may use, and copy the key Recued shows.

## Install

```sh
dsh plugin --profile web add github:recued/dsh-recued
```

That installs it into the Web UI's profile. For a headless run, use `--profile headless`; for
another profile, its name.

Then put the contract's key in `~/.dsh/.env` (or `$DSH_HOME/.env` if you set `DSH_HOME`):

```
RECUED_MCP_TOKEN=<the contract's key>
```

If Recued runs on another computer, add the address the contract's Connect tab shows:

```
RECUED_MCP_URL=https://<your server>/mcp
```

Without `RECUED_MCP_URL`, the plugin connects to `http://127.0.0.1:7717/mcp`, the Recued server on
this computer. Without `RECUED_MCP_TOKEN`, the plugin stays off.

Restart DeepSeek Harness (`dsh web`). Recued's tools appear as `mcp__recued__<tool>`; DeepSeek
Harness may add a short suffix to a name, as in `mcp__recued__work_search_71e96de3b41a`. To check,
ask: "Which notes do I have in Recued?"

## How it works

- The tools you see are the ones the contract grants. To give DeepSeek Harness another tool,
  change the contract in the Recued web app.
- Reading runs at once.
- A change waits for you. Recued answers `awaiting_approval` and tells the agent to have you
  approve it in the Recued app, from the bell at the top of the page. When you have answered, tell
  the agent, and it can ask Recued for the result.
- DeepSeek Harness does not ask before it calls a Recued tool. What a tool may do is decided in
  Recued, by the contract and your approvals.

## Privacy

Your data stays on your Recued server. The model sees only what a tool returns, and Recued
returns only what the contract grants. The key stays in your `.env` file; it is not written into
the plugin or your profile.

## Remove

```sh
dsh plugin --profile web remove dsh-recued
```

## License

MIT
