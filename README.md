# Charon

One local CLI that ferries work items between **Helix ALM, Jira, Gitea, and a team tracker** — so a developer works from one queue instead of four browser tabs.

> Not affiliated with other projects named Charon (including the Aeneas toolchain's Charon). This Charon is a work-item ferry: it carries tickets, issues, and tasks across the systems a team already uses, and never becomes another system of record.

## Why

A typical developer's work is scattered: a defect lives in Helix ALM, a story in Jira, a pull request in Gitea, and the team's own tracking somewhere else. The same work often exists in two or three places at once, slightly out of sync. Charon gives that work **one read model, one search, and one daily list** — while every system of record stays exactly where it is.

Charon is built to be driven both by humans in a terminal and by **GitHub Copilot** (and other MCP clients) as a set of tools.

## Principles

- **Local-first.** Charon runs on the developer's machine. No Charon server, no shared database, nothing to deploy.
- **Read-only by default.** Reads are always safe. Any write to a source system requires an explicit `--apply`.
- **One model, many adapters.** Every source maps into a single `WorkItem` model: `id, source, title, status, assignee, priority, links[], updated`. Adapters are small and independently testable.
- **Machine-readable everywhere.** Every command supports `--json` with a stable schema.
- **Secrets stay out of files.** Tokens come from environment variables or the OS keyring — never from a committed config file.
- **Signed handoffs.** Team-tracker bundles can be exported with a detached **GPG signature** and are verified before import.
- **Boring on purpose.** A single binary, a small command surface, no plugins in v1.

## Commands (planned)

```text
charon today                 # my open items across all sources, deduplicated
charon search <text>         # full-text search across all sources
charon item <source>:<id>    # one item, with links to every source that mentions it
charon sync                  # refresh the local cache from all configured sources
charon link <a> <b>          # record that two items in different systems are the same work
charon mcp serve             # expose the same operations as an MCP server (stdio)
charon export [--sign]       # export a team-tracker bundle, optionally GPG-signed
charon import <bundle>       # verify signature, then import
charon config check          # validate config and test each source connection
```

## Configuration (sketch)

`~/.charon/config.toml` — non-secret settings only:

```toml
[sources.jira]
url = "https://your-team.atlassian.net"
project = "PROJ"
# token read from $CHARON_JIRA_TOKEN or the OS keyring

[sources.gitea]
url = "https://git.your-team.example"
org = "your-org"
# token read from $CHARON_GITEA_TOKEN or the OS keyring

[sources.helix]
url = "https://helix.your-team.example"
# token read from $CHARON_HELIX_TOKEN or the OS keyring
```

## Status

Pre-alpha. Charon is being built **spec-first**: the design is produced and reviewed before code, using the prompt sequence in [`prompts/`](prompts/).

- `prompts/00-spec.md` — Prompt 0: produce `DESIGN.md` (the current step)
- Later prompts (scaffold, adapters, MCP server, GPG bundles) follow once the spec is accepted.

## Non-goals

- No hosted service, accounts, or telemetry.
- No replacing any source system — Charon ferries and unifies; it does not own the record.
- No AI features of its own — Charon is the tool layer that Copilot and other MCP clients call.

## License

To be selected (an OSI-approved license) before the first release.
