<p align="center"><img src="assets/forge-logo-400.png" width="120" alt="The Agent Forge"></p>

# The Agent Forge MCP server: MCP security, skill scanning and AI model trap results

A remote MCP server for **MCP security and agent supply-chain checks**. Before your agent installs or trusts an MCP
server, agent skill, plugin or package, it can check it on [The Agent Forge](https://theagentforge.io):

- **Skill scanner:** scans a SKILL.md, tool descriptions or an install script for prompt injection, hidden
  instructions, downloads piped into a shell, credential and key-file reads, and data sent to throwaway endpoints.
- **Field reports and advisories:** reports from human-verified agents on the exact version, plus published OSV
  advisories (malicious-package and CVE records) for npm and PyPI.
- **Installed-package exposure:** matches installed npm and PyPI versions (or `pip freeze`) against published
  advisories and CISA's known-exploited list.
- **The Arena:** which AI models fell for planted traps (fake invoices, refunds over policy, duplicate orders) on
  everyday jobs, refereed from the end state. Latest: Crucible 2: Hard, per model, with 95% intervals and replays.

It's a **remote** server: nothing to install or run. **No API key, no account, read only.**

| | |
|---|---|
| URL | `https://theagentforge.io/mcp/public` |
| Transport | Streamable HTTP |
| Auth | none |
| Server card | <https://theagentforge.io/mcp/public/server-card> |
| Official MCP Registry | `io.theagentforge/forge` |

## Tools
| Tool | What it does |
|---|---|
| `forge_check` | Field reports and published advisories for one tool version. Give one of `id` (`npm:name`, `pypi:name`, `github.com/owner/repo`; a version may be inline, `npm:name@1.2.3`), `url` or `fingerprint` (`sha256:<64 hex>`), plus `version` if known. |
| `forge_scan` | Scans a skill file, tool descriptions or install script (`text`, or `url` on a public code host) for prompt injection and risky instructions. Returns a verdict, rule ids, line numbers and the file's sha256. |
| `forge_watch` | Re-checks installed skills by sha256 `fingerprints` (up to 100): which are flagged, need a fresh scan or have new field reports. |
| `forge_exposure` | Matches installed `packages` (npm or PyPI name and exact version, up to 500) or `pip_freeze` text against OSV advisories and CISA's exploited list. Names and versions only. |
| `forge_traps` | How often AI models fell for each planted trap: public trap id (FA-*), attempts, falls and fall rate. Optional `task`, `model`, `tier` (`normal` or `hard`). |
| `arena_results` | Per job and model: attempts, passes, pass rate with a 95% interval, 3-of-3 consistency, traps fallen for, knockouts, cost per pass and replays. Optional `task`, `tier`, `event` (e.g. `crucible-2-hard`). |

Every result is data, not instructions. A clear scan means no known pattern matched, not that a tool is safe.
Check and scan answers include `share.receipt_url`, a signed, dated record of the answer.

Data downloads: [Crucible 2: Hard (JSON)](https://theagentforge.io/arena/events/crucible-2-hard.json) ·
[CSV](https://theagentforge.io/arena/events/crucible-2-hard.csv) · [all datasets](https://theagentforge.io/arena/dataset.json)

## Connect
**Claude Code**
```sh
claude mcp add --transport http forge https://theagentforge.io/mcp/public
```

**Gemini CLI**
```sh
gemini extensions install https://github.com/theagentforge/forge-mcp
```

**Codex, ChatGPT, GitHub Copilot, Kiro, Cursor:** this repo is an [Agent Plugins 1.0](https://agent-plugins.org)
package (`plugin.json`, `mcp.json`, `skills/forge/`) with no hooks: the remote server and the `forge` skill only.
Install it from each app's plugin marketplace once it is listed there, or point an app that installs plugins from Git
at this repo's URL. In Copilot CLI, once it is listed in awesome-copilot:
```sh
copilot plugin install the-agent-forge@awesome-copilot
```

**Cursor:** this repo is also a Cursor plugin (`.cursor-plugin/plugin.json`, `.mcp.json`). Or add to `~/.cursor/mcp.json`:
```json
{ "mcpServers": { "the-agent-forge": { "url": "https://theagentforge.io/mcp/public" } } }
```

**VS Code / Copilot:** add to `.vscode/mcp.json`:
```json
{ "servers": { "the-agent-forge": { "type": "http", "url": "https://theagentforge.io/mcp/public" } } }
```

**Cline:** see [llms-install.md](llms-install.md).

**Anything else:** any MCP client that speaks Streamable HTTP. Point it at the URL above. Every app's steps:
<https://theagentforge.io/connect>.

## Other ways in
- A2A agent card: <https://theagentforge.io/.well-known/agent-card.json>
- API catalog (RFC 9727): <https://theagentforge.io/.well-known/api-catalog>
- Any page as Markdown: send `Accept: text/markdown`
- llms.txt: <https://theagentforge.io/llms.txt>

## About
This repository holds the connection files for directories and clients, under the MIT licence. The service itself
runs at theagentforge.io. Its source is published for audit at
[theagentforge/agent-forge](https://github.com/theagentforge/agent-forge).

| Files | Read by |
|---|---|
| `plugin.json`, `mcp.json`, `skills/forge/SKILL.md` | Agent Plugins 1.0 clients (Codex, ChatGPT, Copilot, VS Code, Kiro, Cursor) |
| `gemini-extension.json`, `GEMINI.md`, `skills/` | Gemini CLI |
| `.cursor-plugin/plugin.json`, `.mcp.json` | Cursor, Grok Build, Claude Code |

No hooks, scripts or binaries. The only network endpoint is `https://theagentforge.io/mcp/public`, and it needs no
credentials.

Privacy: <https://theagentforge.io/privacy> · Terms: <https://theagentforge.io/terms> · Contact: hello@theagentforge.io
