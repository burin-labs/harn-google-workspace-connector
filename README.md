# harn-google-workspace-connector

Pure-Harn Google Workspace connector for Drive, Docs, Gmail, and Calendar
workflows.

The initial surface is deliberately connector-shaped, not document-renderer
shaped:

- Normalize Google Drive push channel headers into Harn connector events.
- Poll Drive `changes.list` from a Harn-managed cursor.
- Dispatch common Drive, Docs, and Gmail JSON APIs through an allowlisted
  egress layer.
- Query Calendar availability and events without approval.
- Create and update Gmail drafts independently from sending email.
- Execute Calendar writes and Gmail sends through Harn exact grants, durable
  receipts, at-most-once dispatch, and read-only reconciliation.
- Build deterministic request descriptors for artifact export/import flows.

The document rendering and manifest vocabulary stays in `harn-documents`; this
package owns the external Google Workspace boundary.

## Install

The `v0.1.0` tag contains the initial Drive and Docs surface:

```sh
harn add github.com/burin-labs/harn-google-workspace-connector@v0.1.0
```

Gmail methods currently exist on `main` and are not in a newer tag. Use a path
dependency on this checkout when developing against that unreleased surface.
Use the Harn CLI version pinned in `.harn-version` for validation.

## Configure

- Provider id: `google_workspace`
- API hosts: `www.googleapis.com`, `docs.googleapis.com`,
  `gmail.googleapis.com`
- Required secret: `google_workspace/access-token`
- Required OAuth scopes: `https://www.googleapis.com/auth/drive.file`,
  `https://www.googleapis.com/auth/documents`, and
  `https://www.googleapis.com/auth/gmail.readonly`

Assistant capabilities add these scopes:

| Capability | Google OAuth scope |
| --- | --- |
| Calendar free/busy | `https://www.googleapis.com/auth/calendar.events.freebusy` |
| Calendar event read/write | `https://www.googleapis.com/auth/calendar.events` |
| Gmail draft read/write | `https://www.googleapis.com/auth/gmail.compose` |
| Gmail send | `https://www.googleapis.com/auth/gmail.send` |

Google's `gmail.compose` scope technically permits sending as well as drafting.
The connector still exposes `gmail.drafts.write` and `gmail.send` as different
Harn capabilities and rejects a send attempted with a draft-only action grant.
This application-level separation is load-bearing; OAuth alone cannot express
it. Enabling the new scopes requires an interactive Google consent flow.

Use a Google OAuth client whose application type is **Desktop app**. Then start
browser-based setup with:

```sh
harn connect google_workspace \
  --client-id YOUR_GOOGLE_DESKTOP_CLIENT_ID
```

Harn uses PKCE and Google's public desktop-client flow, so no client secret is
needed or placed in a command. Check the result with:

```sh
harn connect status --connector google_workspace --json
```

## Useful methods

- `drive.files.get`
- `drive.files.list`
- `drive.files.export`
- `drive.files.create`
- `drive.files.update`
- `drive.changes.getStartPageToken`
- `drive.changes.list`
- `drive.changes.watch`
- `docs.documents.create`
- `docs.documents.get`
- `docs.documents.batchUpdate`
- `gmail.messages.list`
- `gmail.messages.get`
- `gmail.messages.attachments.get`
- `calendar.freebusy.query`
- `calendar.events.list`
- `calendar.events.get`
- `calendar.events.create`
- `calendar.events.update`
- `calendar.events.delete`
- `gmail.drafts.list`
- `gmail.drafts.get`
- `gmail.drafts.create`
- `gmail.drafts.update`
- `gmail.drafts.send`
- `gmail.messages.send`
- `artifact.export_request`
- `artifact.import_request`

Read methods and the two artifact request builders use `call(...)`. Every Google
API write deliberately rejects direct calls with `external_action_required`;
legacy `approved` and `_approved` flags grant no authority. Build one adapter
with `external_action_adapter()` and pass it to `external_action_execute(...)`
from `std/external_action`. The production adapter accepts only `live` intents,
matching the service manifest. Tests use mocked transport with live-shaped
intents instead of relabeling Google effects as test operations.

Calendar creates derive a provider event ID from the Harn idempotency key and
store the exact action fingerprint in the event's private extended properties.
Calendar updates accept `etag` and send `If-Match`, preventing a stale assistant
view from overwriting a newer event. Gmail draft/send payloads require both
base64url RFC 2822 `raw` content and `rfc822_message_id`; reconciliation searches
the appropriate Drafts or Sent mailbox without repeating the write.

## Validate

```sh
harn connector check . --provider google_workspace
harn test tests
```

The smoke tests use Harn HTTP mocks and do not call Google APIs.
