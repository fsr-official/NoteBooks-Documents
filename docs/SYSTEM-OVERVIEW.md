# System overview

## Purpose

NoteBooks is a repository-backed learning platform that presents curriculum material as a set of stream workspaces and exposes a small set of community, issues, and admin surfaces around them.

## Architectural boundaries

- Browser shell: static HTML and vanilla JavaScript served by Express
- Runtime API: stream discovery, raw file delivery, session management, and authenticated features
- Data sources: this repository's canonical repository registry, GitHub content repositories, `files.json` manifests, and generated bootstrap artifacts
- Persistence: Upstash Redis in NoteBooks-Framework for active runtime registry/content catalog state; GitHub for source content and update automation

## Runtime composition

1. A page loads the shared shell and route-specific client scripts.
2. The stream shell resolves the current stream slug such as science, commerce, humanities, or developers.
3. The application requests `/api/system/<stream>` and uses `/public/json/<stream>-tree.json` as a bootstrap/offline fallback.
4. The browser renders the repository tree and opens files through the raw delivery pipeline.
5. Content repositories run the reusable workflow in this repository. It validates `files.json` with Git blob SHAs, then sends a signed POST containing the repository, branch, and resulting commit SHA to NoteBooks-Framework.
6. NoteBooks-Framework fetches the manifest at that immutable SHA, diffs per-file SHAs, and publishes changed paths to its runtime catalog. The browser polls for catalog changes and prompts before replacing an open file.
7. Registry changes are similarly delivered by a signed workflow event and activated at runtime without rebuilding or restarting the app.
8. Community, issues, and admin pages use dedicated endpoints instead of being mixed into the content tree flow.

## Invariants

- Generated JSON trees are deployment and offline fallbacks, not the live update channel.
- Runtime registry and content state are held in Upstash Redis; workflow notifications do not trigger app deployment or restart.
- The signed update contract is documented in `CONTENT-UPDATE-CONTRACT.md`.
- The Developers stream is pinned to this repository itself and is not treated like a remote GitHub subject repository.
