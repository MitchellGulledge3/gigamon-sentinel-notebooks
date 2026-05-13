# Notebook output gallery

Real captured outputs from the executed notebooks — the same view you see when
you run them in VS Code against the Sentinel data lake. These PNGs are
referenced from the top-level README and the Security Store package README so
reviewers can preview results without opening Jupyter.

## 01 — Getting started

**Posture summary**
![Posture summary](./01-getting-started-sentinel-datalake-01.png)

**Top application families by flow count**
![App families](./01-getting-started-sentinel-datalake-02.png)

## 02 — Lateral movement investigation

**East-west graph (NetworkX)**
![Lateral movement graph](./02-lateral-movement-investigation-01.png)

## 03 — JA3 fingerprint hunting

**Top JA3 fingerprints by handshakes (histogram)**
![JA3 histogram](./03-ja3-fingerprint-hunting-00-histogram.png)

**Top JA3 client fingerprints (table)**
![JA3 top table](./03-ja3-fingerprint-hunting-01.png)

**Known-bad JA3 matches — 536 hits across 6 threat families** ⚑
![JA3 known-bad](./03-ja3-fingerprint-hunting-02.png)

**Drill-down into sessions matching known-bad JA3s**
![JA3 drilldown](./03-ja3-fingerprint-hunting-03.png)

## 04 — Beacon periodicity analysis

**Top src→dst pairs by flow count**
![Top pairs](./04-beacon-periodicity-analysis-01.png)

## 05 — App mix / shadow IT dashboard

**Application-mix Sankey** (Plotly → exported via kaleido)
![App mix Sankey](./05-app-mix-dashboard-00-sankey.png)

**High-risk shadow IT categories**
![Shadow IT bars](./05-app-mix-dashboard-01.png)

## How these were generated

- `*.png` images embedded in the notebook outputs (matplotlib / NetworkX) were
  base64-extracted directly from the `.ipynb` files.
- The Plotly Sankey JSON in notebook 05 was rendered to PNG with
  `plotly.io.write_image(..., width=1200, height=600)` (requires `kaleido`).
- DataFrame outputs were re-rendered with matplotlib `Axes.table()` for clean
  static previews.

To regenerate after re-executing the notebooks:

```bash
source .venv/bin/activate
pip install -q kaleido
python3 scripts/extract_screenshots.py    # script lives in this folder
```
