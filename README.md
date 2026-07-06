# harn-google-workspace-connector

Pure-Harn Google Workspace connector for Drive and Docs artifact workflows.

The initial surface is deliberately connector-shaped, not document-renderer
shaped:

- Normalize Google Drive push channel headers into Harn connector events.
- Poll Drive `changes.list` from a Harn-managed cursor.
- Dispatch common Drive and Docs JSON APIs through an allowlisted egress layer.
- Build deterministic request descriptors for artifact export/import flows.

The document rendering and manifest vocabulary stays in `harn-documents`; this
package owns the external Google Workspace boundary.

## Provider

- Provider id: `google_workspace`
- API hosts: `www.googleapis.com`, `www.googleapis.com/upload/drive/v3`,
  `docs.googleapis.com`
- Recommended scopes for artifact workflows:
  - `https://www.googleapis.com/auth/drive.file`
  - `https://www.googleapis.com/auth/documents`

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
- `artifact.export_request`
- `artifact.import_request`

## Validation

```sh
harn connector test . --provider google_workspace
```

The smoke tests use Harn HTTP mocks and do not call Google APIs.
