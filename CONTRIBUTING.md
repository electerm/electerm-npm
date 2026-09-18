# Contributing

This repo contains only the npm installer for
[electerm](https://github.com/electerm/electerm) and the workflow that publishes
it. The application source lives in
[electerm/electerm](https://github.com/electerm/electerm).

## Layout

| Path | Purpose |
| --- | --- |
| `npm/electerm` | CLI entry point (`bin`), finds and launches the installed binary |
| `npm/install.js` | `postinstall`: resolves the release asset for the platform/arch and extracts it |
| `npm/utils.js` | HTTP download / tar extraction helpers, GitHub proxy support |
| `test/npm-install-target.spec.js` | arch → release asset resolution table |
| `test/npm-install2.spec.js` | installer, launcher and download helper tests (hits the network) |

`packageRoot` is one level above `npm/`, both here and in the published tarball,
so the installer behaves identically in a checkout and after `npm i -g`.

`README.md` / `README_cn.md` describe this npm installer (what it does,
supported platforms, proxy, upgrade) plus a short "About electerm" section
linking to the main repo. They are not copies of the app README. Keep EN and
CN in sync when editing.

## Development

```bash
npm i --ignore-scripts   # --ignore-scripts: postinstall downloads a full binary
npm run lint
npm test                 # unit tests, no network
npm run test-network     # installer tests that hit httpbin.org
npm run pack-check       # inspect the tarball npm would publish
```

## Publishing

Publishing is driven by the electerm release, never by hand-editing the version
here.

1. A release is published in [electerm/electerm](https://github.com/electerm/electerm).
2. `trigger-npm-publish.yml` there fires a `repository_dispatch`
   (`electerm-release`) at this repo carrying the release version.
3. [`npm.yml`](.github/workflows/npm.yml) stamps that version onto
   `package.json` (`npm version --no-git-tag-version`) and runs
   `npm publish --provenance`.

Nothing is committed back, so `version` in `package.json` is only a placeholder —
the release tag is the source of truth. Re-running the workflow for a version
that is already on npm exits cleanly instead of failing.

Manual paths, both on `npm.yml`:

```bash
# backfill / re-publish a specific version
gh workflow run npm.yml -R electerm/electerm-npm -f version=5.5.16

# or push a tag
git tag v5.5.16 && git push origin v5.5.16
```

### npm trusted publishing

`npm publish` uses OIDC — no `NPM_TOKEN` secret exists. The package's trusted
publisher on npmjs.com must be configured as:

- **Repository:** `electerm/electerm-npm`
- **Workflow filename:** `npm.yml`
- **Environment:** `build`

Renaming the workflow file breaks publishing until the npmjs setting is updated
to match.

The dispatch from the main repo uses the `PAT_REPO_TOKEN` secret (a PAT with
`repo` scope) — the default `GITHUB_TOKEN` cannot trigger workflows in another
repo.
