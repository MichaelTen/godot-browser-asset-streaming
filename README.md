# Godot Browser Asset Streaming Backend - godot-browser-asset-streaming
Use for Godot web export projects with large asset libraries (e.g. 4GB+). Handles on-demand asset streaming, backend setup (Colyseus/Node.js or Python), lazy loading, PCK handling, and browser caching for persistent MMO-like experiences. Triggers: browser optimization, asset streaming, Colyseus integration, reducing initial download size.

A hybrid architecture for running large Godot games (e.g., 4GB+ assets) in the browser with minimal initial download (~38MB core export). Assets stream on-demand from a backend server, with optional persistence.

## Project Goals
- Enable browser-based MMOs or content-heavy Godot games without massive upfront downloads.
- Integrate realtime multiplayer via Colyseus.
- Support Python/Node backends for asset serving and game logic.
- Persistent client storage for logged-out state recovery.

## Architecture
- **Frontend**: Godot HTML5 export + custom asset loader (HTTPRequest + load_resource_pack).
- **Backend**: Colyseus (Node.js) primary for multiplayer + asset streaming. Python (FastAPI) optional for advanced asset management.
- **Streaming**: Dynamic PCK bundles served based on player context (location, progress).

## Feasibility
High. Godot supports runtime resource loading from network. Colyseus has strong Godot support. See `docs/feasibility.md` for details.

## Quick Start (Planning Phase)
1. Clone and explore this repo.
2. Review `docs/` for analysis.
3. Set up Colyseus server.
4. Implement minimal Godot web export with streaming hooks.

## Tech Stack
- Godot 4.x (single-thread web export)
- Colyseus + Godot GDExtension
- Node.js / Python backend
- Browser APIs (IndexedDB, Cache)

## Contributing
Focus on loader scripts, bundlers, and examples.

## License
AGPL - see LICENSE.md
