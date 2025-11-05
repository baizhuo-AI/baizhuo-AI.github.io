# baizhuo-AI.github.io

AI 价格看板

## LLM Pricing Dashboard

This repository hosts a static HTML/CSS/JavaScript application for browsing Large Language Model pricing data. The app consumes the canonical dataset in [`data/official_pricing.json`](data/official_pricing.json) and persists user preferences locally via `localStorage`.

## Features

- Multi-language interface (Simplified Chinese and English) loaded from `/i18n` JSON files.
- Pricing filters: vendor multi-select, search, favorites only, common models only.
- Currency and unit switches (CNY/USD and per-million/per-thousand tokens) using locally stored exchange rates.
- Per-model comment drawer with local persistence and JSON export.
- Temporary CSV/XLSX “test import” for previewing unpublished price sheets in the browser.
- Preferences, favorites, and comments stored locally; official data is immutable in the repository.
- Responsive layout with keyboard friendly controls.

## Getting started

Open [`index.html`](index.html) directly in a browser or serve the project with any static web server (e.g. `python -m http.server`). No build step is required.

### Browser compatibility

The bundled script avoids `async/await`, optional chaining, and other modern syntax so that older Chromium/WebView builds can parse it. Browsers without the Fetch API automatically fall back to `XMLHttpRequest` for loading the JSON assets. If a browser is too old to support `Promise` or `Set`, initialization is aborted with a clear console error instead of leaving a blank screen.

### Deploying to GitHub Pages

If the GitHub UI displays “This branch has not been deployed”, the repository’s Pages configuration still needs to be enabled. A maintainer can set it up with the following steps:

1. Push the latest code to the remote branch that should be published (for example `work` or `main`).
2. In the GitHub web UI open **Settings → Pages**.
3. Under **Build and deployment**, choose **Source → Deploy from a branch** and select the branch (`main` is recommended) with the `/` root folder.
4. Save the configuration. GitHub will queue an initial deployment; the **Actions** tab shows progress. Once the deployment succeeds, the Pages URL appears at the top of the same settings page.

After the first deployment, every push to the selected branch triggers an automatic rebuild.

### Resolving branch conflicts before merging

When opening a Pull Request, GitHub may warn “This branch has conflicts that must be resolved”. This happens when the target branch (e.g. `main`) has new commits that are not in your working branch.

Resolve the conflict locally:

```bash
git checkout work               # switch to your feature branch
git fetch origin                # fetch latest remote refs
git merge origin/main           # or rebase: git rebase origin/main
# edit any files shown as conflicted
git add <resolved-files>
git commit                      # completes the merge or rebase
git push                        # update the PR with the resolution
```

If the branch is rebased instead of merged, use `git push --force-with-lease` for the final step. Once conflicts are cleared, the PR can be merged and the Pages workflow will deploy the combined code.

## Project structure

```
/ (root)
├─ index.html                # Application shell
├─ styles.css                # Tailored styling
├─ app.js                    # Front-end logic (plain script)
├─ /data
│  ├─ official_pricing.json  # Canonical pricing dataset
│  ├─ incoming/              # Drop-zone for CSV updates (maintainers only)
│  └─ tools/                 # Normalization scripts used by GitHub Actions
├─ /i18n                     # Language packs
├─ /docs/admin_guide.md      # Maintainer workflow documentation
└─ .github/workflows         # Automation for dataset updates
```

## Local data import

The “导入测试 / Import test file” action lets operators load CSV or XLSX files in the browser for preview. These files are never uploaded or committed. To promote data into production, follow the maintainer workflow documented in [`docs/admin_guide.md`](docs/admin_guide.md).

## Automation

GitHub Actions workflow `update-data.yml` normalizes CSV uploads in `data/incoming/` into `official_pricing.json` using the Node scripts in `data/tools`. See the admin guide for details.

## Testing

The project currently exposes only static assets, so there is no automated test suite. Manual checks (open `index.html`, try major filters, and inspect the browser console) are sufficient when reviewing changes.
