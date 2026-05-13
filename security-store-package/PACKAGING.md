# Packaging for the Microsoft Security Store (macOS)

This doc walks through producing a Security Store-ready ZIP for the Gigamon
Deep Observability Hunting Pack on macOS. It assumes you've already executed
the notebooks (so the cached outputs are baked in) and just want to rebuild the
upload artifact.

Reference: [Publish a Security Copilot agent or analytics solution in Security Store](https://learn.microsoft.com/security/store/publish-a-security-copilot-agent-or-analytics-solution-in-security-store).

## What's in the package

```
PackageManifest.yaml          # Security Store manifest (type: SentinelLake)
AgentManifest.yaml            # Sentinel platform-solution manifest
README.md                     # Package overview + schedule table
notebooks/
  01-getting-started-sentinel-datalake.ipynb
  01-getting-started-sentinel-datalake.job.yaml
  02-lateral-movement-investigation.ipynb
  02-lateral-movement-investigation.job.yaml
  03-ja3-fingerprint-hunting.ipynb
  03-ja3-fingerprint-hunting.job.yaml
  04-beacon-periodicity-analysis.ipynb
  04-beacon-periodicity-analysis.job.yaml
  05-app-mix-dashboard.ipynb
  05-app-mix-dashboard.job.yaml
```

The pre-built ZIP is checked in at
[`gigamon-sentinel-notebooks-store-package.zip`](./gigamon-sentinel-notebooks-store-package.zip).

## Prerequisites (one time)

```bash
# Confirm zip is installed (it ships with macOS by default)
which zip || xcode-select --install
```

If you want to (re)execute the notebooks before packaging:

```bash
cd ~/gigamon-sentinel-notebooks
python3 -m venv .venv
source .venv/bin/activate
pip install -q -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=180 notebooks/*.ipynb
```

## Rebuild the ZIP — exact commands for macOS

Run these from the repo root:

```bash
# 1. Refresh notebook copies in the package folder (in case you re-executed them)
cp notebooks/*.ipynb security-store-package/notebooks/

# 2. Clean out macOS junk that would otherwise sneak into the archive
cd security-store-package
find . -name ".DS_Store" -delete
rm -f gigamon-sentinel-notebooks-store-package.zip

# 3. Build the archive using the doc's macOS-safe invocation
zip -r gigamon-sentinel-notebooks-store-package.zip . \
    -x ".*" \
    -x "__MACOSX" \
    -x "*.zip"

# 4. Sanity check: listing should contain PackageManifest.yaml at the top level,
#    no .DS_Store, no __MACOSX/, and all five .ipynb + .job.yaml pairs
unzip -l gigamon-sentinel-notebooks-store-package.zip
```

Why those `-x` flags:

| Flag | Excludes |
|---|---|
| `-x ".*"` | hidden dotfiles like `.DS_Store`, `.git*` |
| `-x "__MACOSX"` | Finder's resource-fork sidecar folder |
| `-x "*.zip"` | the archive itself (so re-runs don't nest) |

## Editing schedules or sample data

- Schedule lives in each `.job.yaml`. Change `schedule.type` (`OnDemand` /
  `Scheduled`) and `frequency` (`Hourly` / `Daily` / etc.), then rebuild.
- Spark pool size: edit `sparkPoolSize` (`Small` / `Medium` / `Large`).
- The notebooks themselves are plain Jupyter — edit them, re-execute, copy back
  into `security-store-package/notebooks/`, and rezip.

## Upload to Partner Center

1. Open the [Microsoft Security Store publisher portal](https://securitystore.microsoft.com/).
2. Create a new **platform solution** offer.
3. Upload `gigamon-sentinel-notebooks-store-package.zip`.
4. Start as a **private offer** to validate in your own tenant before promoting
   to public.

## Known caveats

- **`.job.yaml` schema isn't formally documented.** The VS Code Sentinel
  extension generates these interactively. The fields used here (`name`,
  `description`, `notebook`, `sparkPoolSize`, `schedule`) match what the
  extension exposes. If Partner Center rejects a field, open the notebook in VS
  Code → right-click → *Microsoft Sentinel → Create schedule Job* → download
  the generated `.job.yaml` and overwrite ours.
- **`AgentManifest.yaml` for notebook-only packages.** The Security Store doc
  lists `SentinelLake` as a standalone type. The Sentinel platform-solution doc
  says every solution needs an `AgentManifest.yaml`. We include a minimal one
  to satisfy both.

## One-liner

If you just want to rebuild the zip and don't care about the prose:

```bash
cd ~/gigamon-sentinel-notebooks/security-store-package && \
  cp ../notebooks/*.ipynb notebooks/ && \
  find . -name ".DS_Store" -delete && \
  rm -f gigamon-sentinel-notebooks-store-package.zip && \
  zip -r gigamon-sentinel-notebooks-store-package.zip . -x ".*" -x "__MACOSX" -x "*.zip" && \
  unzip -l gigamon-sentinel-notebooks-store-package.zip
```
