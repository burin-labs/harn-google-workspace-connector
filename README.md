# harn-google-workspace-connector

Pure-Harn Google Workspace connector for Drive, Docs, and Gmail artifact
workflows.

The initial surface is deliberately connector-shaped, not document-renderer
shaped:

- Normalize Google Drive push channel headers into Harn connector events.
- Poll Drive `changes.list` from a Harn-managed cursor.
- Dispatch common Drive, Docs, and Gmail JSON APIs through an allowlisted
  egress layer.
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

Start browser-based setup with `harn connect google_workspace`. Check its
status with `harn connect status --connector google_workspace --json`.

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

## Validate

```sh
harn connector test . --provider google_workspace
```

The smoke tests use Harn HTTP mocks and do not call Google APIs.
