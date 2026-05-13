# Gigamon × Microsoft Sentinel — Jupyter Notebooks

A companion repo to [`gigamon-sentinel-mcp-demo-ui`](https://github.com/MitchellGulledge3/gigamon-sentinel-mcp-demo-ui) that demonstrates how a security team can **explore** the same Gigamon CCF data that the MCP tools answer questions about.

## Why notebooks alongside MCP tools?

| Surface | When you reach for it |
| --- | --- |
| **MCP tools** in Security Copilot | "Are any TLS clients fingerprinting as Cobalt Strike?" — one prompt, one answer. |
| **Notebooks in this repo** | "Walk me through every JA3 fingerprint we saw, cluster the rare ones, and let me drill into the top 5 outliers." — open exploration. |

Same source table: `GigamonCcfMcpDemo_CL`. Same workspace ID. Different audience and different cognitive mode.

---

## The 5 notebooks

1. **`01-getting-started-sentinel-datalake.ipynb`** — Auth with `DefaultAzureCredential`, connect to Log Analytics via `azure-monitor-query`, run your first KQL.
2. **`02-lateral-movement-investigation.ipynb`** — Pull east-west SMB/RDP/SSH flows, build a NetworkX directed graph, and plot the lateral-movement topology.
3. **`03-ja3-fingerprint-hunting.ipynb`** — Histogram every TLS JA3 you've seen, cluster rare fingerprints, and drill into known-bad hits.
4. **`04-beacon-periodicity-analysis.ipynb`** — Per-flow inter-arrival times, FFT for periodicity, jitter/IQR per (src,dst,port).
5. **`05-app-mix-dashboard.ipynb`** — Plotly Sankey: `app_family → app_name → bytes`. Shadow IT bars. Drop into a leadership deck.

Each notebook is self-contained and uses **only** the Gigamon CCF schema.

---

## Quick start

```bash
git clone https://github.com/MitchellGulledge3/gigamon-sentinel-notebooks.git
cd gigamon-sentinel-notebooks
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env       # fill in WORKSPACE_ID
az login
jupyter lab
```

## Configuration

`.env` keys (see `.env.example`):

| Key | Required | Notes |
| --- | --- | --- |
| `WORKSPACE_ID` | yes | Log Analytics workspace GUID for your Sentinel data lake. |
| `TENANT_ID` | optional | Override the Azure CLI default tenant. Gigamon onboarded tenant: `0927b619-f6e4-4461-bcda-333194268117`. |
| `TIMERANGE_HOURS` | optional | Default `24`. |

---

## How this fits the broader story

```mermaid
flowchart LR
  G[Gigamon GigaVUE / AMX] -- DPI flows --> CCF[Sentinel CCF connector]
  CCF --> T[(GigamonCcfMcpDemo_CL)]
  T --> M[MCP tools  ·  Copilot]
  T --> N[Notebooks  ·  hunters]
  M --> A[Agent HQ / Defender portal]
  N --> R[Reports + exec dashboards]
```

The 8 MCP tools in the sibling repo and the 5 notebooks here both read the same table. Investing in the CCF connector pays off twice.

---

## License

MIT. See [`LICENSE`](./LICENSE).
