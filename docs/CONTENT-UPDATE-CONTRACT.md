# Runtime repository and content updates

The `NoteBooks-Documents` repository owns the canonical `GITHUB-REPOSITORIES.md` registry. The NoteBooks app consumes registry and content updates at runtime; a content update does not require an app deployment.

## Signed update request

Send `POST /api/webhooks/content-update` with a JSON body:

```json
{
  "schemaVersion": 1,
  "kind": "content",
  "repository": "fsr-science/NCERT-Science",
  "branch": "main",
  "commitSha": "full-40-character-commit-sha"
}
```

For registry changes, use `"kind": "registry"`, set `repository` to the configured documents repository, and send the commit containing `GITHUB-REPOSITORIES.md`.

Sign the exact UTF-8 request body with HMAC-SHA256 using the shared `NOTEBOOKS_UPDATE_SECRET`. Send the signature as `X-NoteBooks-Signature: sha256=<hex-digest>` and the JSON content type. The app rejects unsigned, malformed, unsupported, or unregistered updates. Do not send file contents or credentials in the request.

The commit SHA is a content locator, not a manually managed release version. Content updates are processed only if the reported SHA is still the branch head; the app reads `files.json` at that immutable SHA. Registry notifications resolve to the current head of the configured documents branch so a later unrelated documentation commit cannot cause a registry change to be skipped. Enabled repositories in the `science`, `commerce`, `humanities`, and `developers` streams can send content updates on their configured branch. Repositories must include the SHA and MIME metadata for files in `files.json`; Git blob `sha` values are used to detect exact file changes. The app records the processed repo commit even when no served file changed, but does not publish a content update in that case.

The first content notification after a repository is registered establishes its SHA baseline and does not publish file changes. Later notifications publish added, modified, and removed paths by comparing Git blob SHAs.

The content endpoint returns after processing the update. A successful response includes the central catalog version and whether served content changed. Failed verification or processing must be treated as a failed workflow run so the update can be retried.

## Content repository workflow setup

Each enabled content repository should invoke the reusable workflow on pushes to its configured branch. This includes `NoteBooks-Documents`, which publishes the Developers stream. The workflow regenerates and validates `files.json` (including each file's Git blob SHA), commits it only when needed, and reports the resulting commit SHA:

```yaml
name: Notify NoteBooks of content updates

on:
  push:
    branches: [main]

permissions:
  contents: write

jobs:
  notify:
    uses: fsr-official/NoteBooks-Documents/.github/workflows/reusable-content-update.yml@main
    secrets:
      NOTEBOOKS_UPDATE_URL: ${{ secrets.NOTEBOOKS_UPDATE_URL }}
      NOTEBOOKS_UPDATE_SECRET: ${{ secrets.NOTEBOOKS_UPDATE_SECRET }}
```

Add repository Actions secrets:

- `NOTEBOOKS_UPDATE_URL`: the app endpoint URL, ending in `/api/webhooks/content-update`
- `NOTEBOOKS_UPDATE_SECRET`: the shared HMAC secret configured on the app

The reusable workflow needs `contents: write` permission so it can commit a changed manifest. It uses `[skip ci]` for the generated-manifest commit and sends the SHA of the final commit, so the app fetches a stable snapshot instead of reading a moving branch name.

## Client behavior

Clients poll the app's catalog version and receive only published content changes. A client updates its tree and cache references without reloading the application shell. When a file currently open in a reader has a new blob SHA, the reader stays unchanged and shows an explicit update action. Applying it fetches the immutable new revision and preserves the reader window and position; unrelated files and user work are not replaced.
