# SPEC Overview Dashboard

A browser-based dashboard for exploring **SPEC data files** from synchrotron
beamline experiments. Built with FastAPI and Plotly, it lets you browse the
experiment directory tree, load SPEC files, inspect per-scan metadata and motor
positions, plot any column against any other, fit peaks, compare two files, and
export results — all from a single self-contained Python application.

Developed for the **QM² beamline (CHESS ID4B)**, but it works with any standard
SPEC-format data file.

---

## Features

- **Directory browser** — navigate the experiment file tree and detect SPEC files automatically (fast header sniffing, so large trees stay responsive).
- **Scan info table** — view every scan in a file with its command, point count, motors, and metadata parsed from SPEC headers (`#S`, `#L`, `#O/#o`, `#P`, etc.).
- **Motor positions** — read per-scan motor positions from the SPEC header.
- **Interactive plotting** — pick X and Y columns, overlay multiple scans, with optional normalization and log scaling, rendered with Plotly.
- **Peak fitting** — fit a scan's curve with a Gaussian or Lorentzian model (via SciPy `curve_fit`) for quick quantitative readouts.
- **Two-file comparison** — load a second SPEC file and plot the two side by side.
- **Folder timeline & subfolder discovery** — summarize scans across a folder and locate where specific scan data lives.
- **CSV export** — download selected scans/columns or the full dataset.
- **Keyboard shortcuts** — Ctrl+O (browse), Ctrl+P (plot), Ctrl+T (table), Ctrl+E (export).

---

## Requirements

- Python 3.8+
- Python packages:

```
fastapi
uvicorn[standard]
pandas
numpy
plotly
scipy
```

---

## Installation

```bash
git clone https://github.com/<your-username>/spec-overview-dashboard.git
cd spec-overview-dashboard
pip install -r requirements.txt
```

Or install the dependencies directly:

```bash
pip install "fastapi" "uvicorn[standard]" pandas numpy plotly scipy
```

---

## Running the dashboard

```bash
python spec_dashboard_v1.py
```

The server starts on:

```
http://localhost:8000
```

By default it binds to `127.0.0.1` (local only) and browses from the root
`/nfs/chess/id4b/`. Open the URL in your browser and use the directory browser
to locate a SPEC file, or set a different root from within the UI (`/set_root`).

### Running on a remote beamline machine

The dashboard binds to localhost, so from your laptop use SSH port forwarding to
reach it securely:

```bash
ssh -L 8000:localhost:8000 you@lnx201.classe.cornell.edu
# then run the dashboard on the remote machine and open http://localhost:8000 locally
```

If you need it reachable directly on the network instead, change the last line
of `spec_dashboard_v1.py` to bind `host="0.0.0.0"` — but only do this on a
trusted network, since the CORS policy is open (`allow_origins=["*"]`).

---

## API routes

The app is a single FastAPI service; the root route serves the web UI and the
rest are JSON/stream endpoints it calls.

| Route | Method | Purpose |
|---|---|---|
| `/` | GET | Web UI (HTML) |
| `/set_root` | POST | Set the browsing root directory |
| `/browse`, `/browse_abs` | POST | List a directory / browse by absolute path |
| `/load_file` | POST | Parse and load a SPEC file |
| `/load_sample_data` | POST | Load bundled/sample data |
| `/data_info`, `/scan_info` | GET | Dataset summary / per-scan info table |
| `/motor_positions` | GET | Motor positions for the loaded file |
| `/fit` | POST | Gaussian/Lorentzian peak fit |
| `/plot` | POST | Build a Plotly figure for selected scans/columns |
| `/export_csv`, `/export_info` | POST/GET | Export data as CSV / export metadata |
| `/file_status`, `/reload_file` | GET/POST | Check for changes / reload the file |
| `/folder_timeline` | POST | Summarize scans across a folder |
| `/spec_subfolders`, `/find_scan_data` | POST | Discover SPEC subfolders / locate scan data |
| `/load_file2`, `/data_info2`, `/plot2`, `/scan_info2` | POST/GET | Second-file comparison endpoints |

---

## Architecture notes

- **Single-file app** — all Python logic and the full HTML/CSS/JS front end live in `spec_dashboard.py`; the UI is served from the `/` route as an embedded template.
- **Fast SPEC detection** — `is_likely_spec_file()` reads only the first 512 bytes and counts SPEC header markers, so scanning big directories on NFS is cheap.
- **In-memory data stores** — parsed data is held in `data_store` (and `data_store_2` for comparison); no database is required.
- **Plotly rendering** — figures are generated server-side with `plotly.graph_objects` and rendered in the browser.

---

## License

Suggested license: **MIT** (see the `LICENSE` file). This is beamline-developed
software, so please confirm the appropriate license and any redistribution
policy with the CHESS / QM² beamline staff before publishing.

---
## Beamline Access

## Architecture notes

- ** Beamline environment** - `source /nfs/chess/sw/qm2_SPEC/bin/activate`
- **File location** — `cd /nfs/chess/id4baux/suchi/2026/nxrefine_data_analysis/SPEC_dashboard/final` 
- **run code** —  `python spec_dashboard.py` 

---

## Acknowledgements

Developed for the **QM² (Quantum Materials) beamline, CHESS ID4B**, Cornell High
Energy Synchrotron Source. Built with [FastAPI](https://fastapi.tiangolo.com/),
[Plotly](https://plotly.com/python/), pandas, NumPy, and SciPy.
