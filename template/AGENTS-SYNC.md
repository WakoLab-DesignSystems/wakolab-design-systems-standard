# Agent synchronization — A2A 1.0 message storage

This is a **repository-local, Git-backed convention**, not an official A2A filename, storage specification or live A2A endpoint. It follows the same convention as the other WakoLab design-system repositories, with no dependency on them.

Follow [AGENTS.md](AGENTS.md) before answering or modifying work. Use the [A2A v1.0 specification](https://a2a-protocol.org/v1.0.0/specification/) for message objects and roles.

## Conversation files

- `.a2a/project.jsonl`: repository-level coordination, priorities and questions.
- `.a2a/tasks/<source>-<id>.jsonl`: the conversation for one issue or ticket, created **only when a real question or answer exists** (for example `github-12.jsonl` for issue #12 of this repository). Use lowercase ASCII letters, digits, hyphens and underscores; keep names stable.
- `.a2a/tasks/.gitkeep`: a placeholder for the empty directory; not an A2A artifact.

Each JSONL line is one **complete A2A v1.0 `Message` object** with a unique `messageId`, a `contextId` that stays stable for that conversation, a `role` (`ROLE_USER` or `ROLE_AGENT`) and `parts`. Roles describe the sides of an exchange, not verified identities. Optional `metadata` may name the authoring and recipient roles, the source issue and the commits concerned. A GitHub issue number is not an A2A `taskId`.

## Rules

- **Append only.** Never rewrite, delete, reorder, summarize or translate earlier lines; a correction is a new message. Git does not enforce this, so review diffs of `.a2a/` carefully.
- Each repository and each conversation has its own `contextId`; do not reuse another repository's.
- Do not import history from other repositories. Link to it instead.
- Record only accessible, authorized content. Never commit secrets, hidden prompts, private reasoning or unconsented confidential conversation. State missing history rather than inventing it.
- A2A `Task`, `Artifact` and other non-message events are not stored as `Message` lines.
- Participation needs no registration, token or reward agreement.
- No agent is notified when a commit appears. Mention handoffs in the related issue or pull request.

## Initialization record

`.a2a/project.jsonl` starts with one initialization message (`messageId` `<NEW-UUID>`, `contextId` `<NEW-UUID>`), written when this repository was created. It marks the start of the log; it is not a record of any earlier conversation. Generate fresh identifiers for this repository; never reuse another repository's.
