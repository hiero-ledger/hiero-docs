# hiero-docs workspace guidance

## Link integrity rule

**Before committing any `.md` file changes, verify every relative link in every modified
file resolves to an existing file on disk.**

How to check:
1. For each modified `.md` file, extract all markdown links `[text](path)` and HTML
   `<a href="path">` attributes where `path` does not start with `http` or `#`.
2. Resolve each path relative to the containing file's directory.
3. Check that the resolved path exists in the working tree.
4. Fix any broken links before committing — do not leave them for CI.

Use the `/check-links` skill for a full-repo audit at any time.

## GitBook path resolution

Card tables in section landing pages (files in `block-node/*.md`) use HTML
`<a href="...">` inside `data-view="cards"` tables. GitBook resolves those hrefs
**relative to the file's own directory**, not the repo root. A landing page at
`block-node/deployment.md` targeting the file `block-node/block-node/operations/foo.md`
must link to `block-node/operations/foo.md` — **not** `block-node/block-node/operations/foo.md`,
which GitBook resolves one level too deep and produces a 404.
