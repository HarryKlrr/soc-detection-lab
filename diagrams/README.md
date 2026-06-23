# Diagrams

Architecture, workflow, and detection diagrams for this project.

## Diagrams

- [x] [Lab network topology](network-topology.md) — host-only network, VM IPs
- [x] [SOC detection workflow](soc-workflow.md) — log collection → alert → triage → investigation → response, including the IR-006 search-verification lesson
- [x] [MITRE ATT&CK Navigator layer](mitre-navigator-layer.json) — covered techniques across IR-001–IR-009, color-coded by detection status
- [x] [Wazuh architecture](wazuh-architecture.md) — manager, agent, dashboard, archives.json

All four are written as Mermaid diagrams (render natively on GitHub) plus one real Navigator layer JSON, rather than PNG/draw.io exports — the underlying data (IPs, rule IDs, log channels, MITRE technique IDs) is already documented elsewhere in this repo, so keeping diagrams as version-controlled plain text makes them trivial to update if the lab changes.

## Tools

- [draw.io](https://app.diagrams.net/) — free, browser-based diagramming, if a more visual export is wanted later
- [Excalidraw](https://excalidraw.com/) — lightweight whiteboard-style diagrams
- MITRE ATT&CK Navigator — [https://mitre-attack.github.io/attack-navigator/](https://mitre-attack.github.io/attack-navigator/) — load [`mitre-navigator-layer.json`](mitre-navigator-layer.json) here ("Open Existing Layer" → "Upload from local") to view it interactively
