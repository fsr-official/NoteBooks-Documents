# Repository and files.json model

## Registry model

The canonical repository registry is declared in this repository's `GITHUB-REPOSITORIES.md`. A signed update notification lets the NoteBooks app fetch and validate a specific registry commit and activate it at runtime. The app may still use generated artifacts as a bootstrap fallback, but deployed content updates do not require rebuilding those artifacts.

Each entry includes:

- name
- stream
- repo
- branch
- root
- enabled
- priority
- pages
- empty
- LFS
- optional `fallback` (`github-tree`) for repositories without `files.json`

The build script reads this registry and generates public/json/repo-registry.json and per-stream manifest trees.

## Manifest generation

The generation pipeline forms a repository manifest by reading a repo’s files.json or Pages manifest, normalizing raw paths, and folding the result into a stream-scoped tree. The root node is then emitted as a JSON artifact such as public/json/science-tree.json.

Enabled repositories report their commit SHA using the signed workflow described in [CONTENT-UPDATE-CONTRACT.md](CONTENT-UPDATE-CONTRACT.md). The app fetches `files.json` at that immutable commit, compares the manifest's Git blob SHAs, and refreshes only the affected stream/catalog state. A commit that changes no served files advances the processed commit without prompting readers to update content.

NoteBooks-Documents is listed in the Developers stream and publishes its own `files.json`; runtime updates compare its Git blob SHAs like other enabled repositories. Its registry entry retains `fallback: github-tree` for generated build artifacts or startup when a manifest is unavailable. The generator requests the recursive Git tree, converts blob entries (including blob SHAs) into the same repository tree shape, and combines it with the Framework's local Developers tree. The runtime stream API also retains that local Framework tree when rebuilding a Developers tree from the active registry. GitHub's truncated-tree response is rejected rather than being presented as a complete index.

## Local developer workspace

The Developers workspace is intentionally different: it is rooted in the current repository’s local files.json. That keeps contributor docs, monitors, notes, and maintainer resources visible in the same shelf without creating a fake remote repo entry.
