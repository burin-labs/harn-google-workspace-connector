# CLAUDE.md

See `AGENTS.md` and the canonical Harn connector authoring guide:

- https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md

## Provider notes

- Keep Google Workspace API calls scoped to `googleapis.com` hosts.
- Prefer `drive.file` and `documents` scopes for user-created artifact flows.
- Treat push notification headers as hints; Drive channel messages are not a
  replacement for polling `changes.list` with a stored page token.
