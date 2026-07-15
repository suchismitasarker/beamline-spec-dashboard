# Changelog

All notable changes to this project are documented here. The format is loosely
based on [Keep a Changelog](https://keepachangelog.com/).

## [1.0.0] - 2026-07-15

### Added
- Initial public release of the SPEC Overview Dashboard (`spec_dashboard_v1.py`).
- FastAPI service with an embedded web UI served at `/`.
- Directory browser with fast SPEC-file detection.
- SPEC parsing: per-scan info table, motor positions, and header metadata.
- Interactive Plotly plotting with multi-scan overlay, normalization, and log scaling.
- Gaussian / Lorentzian peak fitting via SciPy `curve_fit`.
- Two-file comparison mode.
- Folder timeline and subfolder/scan-data discovery.
- CSV and metadata export.
- Keyboard shortcuts: Ctrl+O (browse), Ctrl+P (plot), Ctrl+T (table), Ctrl+E (export).
