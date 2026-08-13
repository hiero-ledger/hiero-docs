# Check Links

Scan every `.md` file in the hiero-docs repository for broken relative links, then fix
each one automatically where possible and flag the rest for manual review.

## Steps

### 1. Discover files

Find all `.md` files under the working directory:
```
find . -name "*.md" -not -path "./.git/*"
```

If the user passes a path argument (e.g. `/check-links block-node/`), scope the search
to that subtree only.

**Always skip these paths** — they are design/test/architecture docs that contain
deliberate links to source-code files (`.kts`, `.proto`, `.svg`) that do not exist in
hiero-docs. They are excluded from the CI check for the same reason:
- `block-node/design/`
- `block-node/test-plan/`
- `block-node/block-node/architecture/plugins.md`

### 2. Extract links from each file

For each file, extract:
- Markdown links: `[text](path)` — capture the `path` part
- HTML href attributes: `<a href="path">` — capture `path`

Skip links where `path`:
- starts with `http://` or `https://` (external — not checked here)
- starts with `#` (anchor-only)
- starts with `mailto:`

### 3. Resolve and check each link

For each internal link `path` found in file `F`:
1. Strip any `#anchor` suffix before resolving (check the file, not the heading).
2. Resolve `path` relative to `F`'s directory: `dir(F) / path`.
3. Normalize the resolved path (collapse `../`, `./`).
4. Check whether the resolved path exists in the working tree.
5. Record result: **OK** or **BROKEN** (with the resolved path).

### 4. Fix broken links automatically

For each BROKEN link, attempt auto-fix:
1. Take the filename from the broken target (e.g. `single-node-k8s-deployment.md`).
2. Search the working tree for a file with that exact name:
   ```
   find . -name "single-node-k8s-deployment.md" -not -path "./.git/*"
   ```
3. **Unique match:** compute the correct relative path from `F`'s directory to the
   found file and apply the fix directly with the Edit tool.
4. **Multiple matches:** list the candidates — do not guess. Ask the user which one
   is correct before editing.
5. **No match:** report as unresolvable — the target file may not exist yet (pending
   PR, deprecated page). Flag it clearly.

### 5. Report

After all fixes are applied, print a summary:
```
Link check complete
  Files scanned:   <N>
  Links checked:   <N>
  Fixed:           <N>  (list each: file → old path → new path)
  Needs review:    <N>  (list each: file → broken path → reason)
  Clean:           <N>
```

If everything is clean, say so and stop. Do not suggest committing — that decision
belongs to the user.

## Important rules

- **Never fix external links** — only relative internal links.
- **Never guess at a fix when multiple candidates exist** — list them and ask.
- **Do not commit** — this skill only edits files. Committing requires the user's
  explicit approval per the project workflow.
- Run `git diff --stat` after fixing to give the user a quick diff overview.
