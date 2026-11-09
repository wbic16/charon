# Prompt 0 — Produce the design spec (no code yet)

Paste everything below the line into GitHub Copilot Chat (or your MCP-capable
assistant of choice), in an empty repository containing only `README.md` and
this file. Prompt 0 produces **one file: `DESIGN.md`**. Do not let it scaffold
code — the spec is reviewed first, and every later prompt builds on it.

---

You are designing a new open-source CLI called **Charon**. Read `README.md`
first and treat it as the product brief. Your only deliverable in this session
is a file named `DESIGN.md`. Write no application code.

## What Charon is

A local-first, single-binary CLI that unifies a developer's work items from
**Helix ALM, Jira, and Gitea**, plus a simple file-based **team tracker**,
into one model. It is used by humans in a terminal and by GitHub Copilot via
an MCP server. It is a streamlined tool for a small development team — favour
the simplest design that fully works.

## Hard constraints

1. **Local-first.** No server component, no shared database, no accounts, no
   telemetry. State lives in the user's home directory.
2. **Read-only by default.** Any operation that writes to a source system must
   be behind an explicit `--apply` flag and must print exactly what it will
   change before doing it.
3. **One model.** Define a single `WorkItem` struct:
   `id, source, external_id, title, status, assignee, priority, labels[],
   links[], created, updated`. Every source system maps into this model and
   nothing else leaks through the adapter boundary.
4. **Secrets.** API tokens are read from environment variables or the OS
   keyring only. The config file (`~/.charon/config.toml`) must be safe to
   commit to a dotfiles repo — design it so it can never contain a secret.
5. **Stable JSON.** Every CLI command supports `--json`. The JSON schema for
   `WorkItem` is a public contract; version it.
6. **GPG.** Team-tracker bundles are exported as a file plus a detached GPG
   signature, and verified before import. Use the system `gpg` or a
   well-maintained library — do not design any cryptography.
7. **Non-goals** (state them back in the spec): no hosted service, no
   replacing any source system, no built-in AI features, no plugin system
   in v1, no persona or agent-identity features of any kind.

## DESIGN.md must contain

1. **Overview** — one page: what Charon does, the architecture in one diagram
   (ASCII is fine), and the non-goals.
2. **The WorkItem model** — field-by-field table with types, plus the mapping
   from each source's native fields (Helix ALM item, Jira issue, Gitea
   issue/PR) into it. Where a source has no equivalent field, say so.
3. **Adapter interface** — the exact operations every adapter implements
   (`list`, `get`, `search`, `comment`, `transition`), which are read vs.
   write, and how each source's API authenticates. Name the real API for
   each: Jira Cloud REST v3, the Gitea REST API, the Helix ALM REST API.
   Flag anything you are unsure about as an open question rather than
   guessing an endpoint.
4. **Deduplication** — how Charon decides that a Helix item, a Jira issue,
   and a Gitea PR are the same work: explicit `charon link`, cross-references
   in titles/bodies (e.g. a Jira key inside a Gitea PR title), and manual
   confirmation. Dedup must never silently merge — propose, then confirm.
5. **Local cache** — what is cached, where, in what format (prefer plain
   files or SQLite — justify the choice), and how `charon sync` refreshes it
   incrementally using each source's `updated` timestamp.
6. **Command surface** — every command from the README, with flags, example
   output (human and `--json`), and exit codes.
7. **MCP server** — how `charon mcp serve` exposes the same operations as
   MCP tools over stdio, with the same read/write permission model
   (write tools refuse to run unless `--apply` was passed at serve time).
8. **Team tracker + GPG bundles** — the tracker's file format, the bundle
   format, the sign/verify flow, and what happens on a bad signature
   (import aborts, nothing is partially applied).
9. **Tech choice** — recommend ONE language/toolchain for a single static
   binary that runs on Windows, macOS, and Linux, and justify it in five
   bullet points or fewer against the runner-up.
10. **Test strategy** — adapters are tested against recorded/mocked API
    responses; no test may call a live system.
11. **Open questions** — every decision you could not make from this prompt,
    listed at the end, numbered, each with your recommended default.

## Acceptance check

End `DESIGN.md` with a short checklist mapping each hard constraint above to
the section of the design that satisfies it. If any constraint cannot be
satisfied as stated, stop and say which one and why, instead of quietly
weakening it.

Do not scaffold the project, do not write code files, and do not choose a
license. Deliverable: `DESIGN.md` only.
