# AGENTS.md

Shared connector authoring rules live in the Harn guide:

- [Connector authoring guide](https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md)

Put shared connector guidance in the Harn guide and keep only Google
Workspace-specific notes here.

## Provider notes

- Keep API calls scoped to the Google API hosts allowlisted in `src/lib.harn`.
- Use the exact Drive, Docs, and Gmail scopes declared in `harn.toml`.
- Treat Drive push headers as wake-up signals; the stored `changes.list` page
  token is the durable source of change details.
