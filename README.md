# harn-google-workspace-connector

Pure-Harn Google Workspace connector for Drive, Docs, and Gmail artifact workflows.

The initial surface is deliberately connector-shaped, not document-renderer
shaped:

- Normalize Google Drive push channel headers into Harn connector events.
- Poll Drive `changes.list` from a Harn-managed cursor.
- Dispatch common Drive, Docs, and Gmail JSON APIs through an allowlisted egress layer.
- Build deterministic request descriptors for artifact export/import flows.

The document rendering and manifest vocabulary stays in `harn-documents`; this
package owns the external Google Workspace boundary.

## Provider

- Provider id: `google_workspace`
- API hosts: `www.googleapis.com`, `www.googleapis.com/upload/drive/v3`,
  `docs.googleapis.com`, `gmail.googleapis.com`
- Recommended scopes for artifact workflows:
  - `https://www.googleapis.com/auth/drive.file`
  - `https://www.googleapis.com/auth/documents`
  - `https://www.googleapis.com/auth/gmail.readonly`

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
- `artifact.export_request`
- `artifact.import_request`

## Validation

```sh
harn connector test . --provider google_workspace
```

The smoke tests use Harn HTTP mocks and do not call Google APIs.
