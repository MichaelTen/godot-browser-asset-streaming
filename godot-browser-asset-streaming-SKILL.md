---
name: godot-browser-asset-streaming
description: Use for Godot web export projects with large asset libraries (e.g. 4GB+). Handles on-demand asset streaming, backend server setup with Colyseus/Node.js or Python, lazy loading, PCK handling, browser storage/caching for persistent MMO-like experiences. Triggers include browser export optimization, asset streaming, Colyseus integration for Godot, reducing initial download size.
---

# Godot Browser Asset Streaming Skill

## Overview
This skill guides implementation of a hybrid frontend-backend architecture for Godot games exported to HTML5/WebAssembly, where the core game (~38MB export) streams large assets (e.g., 4GB total) on-demand from a server. Perfect for isometric/UO-style MMOs or content-heavy games running in browser with persistence via IndexedDB or similar.

Focus on keeping initial load small while enabling seamless streaming, multiplayer sync via Colyseus, and optional Python backend for asset management.

## When to Use
- Godot project with massive assets unsuitable for full PCK embed.
- Browser export where initial download must be <50MB.
- Need for dynamic asset loading based on player location/spawn.
- Integration with Colyseus for realtime multiplayer.
- Persistent storage of streamed assets client-side.

## Core Architecture
1. **Godot Web Export**:
   - Use single-threaded export (Godot 4.3+) for broad compatibility.
   - Do **not** embed full PCK; keep minimal core assets.
   - Implement custom loading with `HTTPRequest` + `ProjectSettings.load_resource_pack` or runtime resource loading for .pck/.zip patches.
   - Use `ResourceLoader.load_threaded_request` for prefetching.

2. **Backend Server**:
   - **Primary Recommendation: Node.js + Colyseus**
     - Colyseus has official Godot SDK/GDExtension support.
     - Excellent for authoritative multiplayer rooms, state sync, matchmaking.
     - Stream assets via WebSocket/HTTP endpoints (serve .pck files, textures, scenes on-demand).
     - Python not strictly needed; Node excels at streaming and real-time.
   - **Python Option** (e.g., FastAPI/Flask + WebSockets):
     - For asset indexing, metadata DB (SQLite/Postgres), custom streaming logic.
     - Use `aiohttp` or `fastapi` for efficient file serving with range requests.
     - Integrate with Colyseus if hybrid, but pure Node is simpler for multiplayer.

3. **Asset Streaming Flow**:
   - Core export includes player spawn area assets.
   - On player movement/spawn: Client requests relevant .pck bundles via HTTP.
   - Server streams compressed files (gzip/Brotli).
   - Godot loads packs dynamically: `ProjectSettings.load_resource_pack(temp_path)`.
   - Cache in browser (IndexedDB, Cache API) for persistence across sessions.
   - Prefetch based on proximity or predicted paths.

## Feasibility Analysis
**High Feasibility** for core streaming:
- Godot supports runtime PCK loading from network (see forum examples, HTTPRequest + load_resource_pack).
- Server-side compression reduces 4GB significantly (WASM/PCK gzip well).
- Colyseus integration mature for Godot (GDExtension beta, but functional).
- Challenges:
  - Browser CORS, same-origin, SharedArrayBuffer headers if using threads.
  - Many small requests can bottleneck; bundle into scene-specific PCKs.
  - VRAM/texture management; consider mip streaming if available.
  - Persistence: Use Godot's `user://` with browser FS or JS interop for IndexedDB.
- Python backend viable but adds complexity; stick to Node/Colyseus unless heavy data processing needed (e.g., procedural gen).

**Pros**:
- Tiny initial download.
- Scalable for MMOs.
- Lazy loading improves UX.

**Cons**:
- Network dependency (offline fallback needed).
- Implementation requires custom loader scripts.
- Testing in browser with large files.

See `/docs/feasibility.md` for detailed report.

## Implementation Steps
1. Export minimal Godot project to Web.
2. Set up Colyseus server (`npm create colyseus-app`).
3. Implement Godot client SDK for multiplayer + asset requests.
4. Create asset bundler script (Python or Node) to split assets into loadable PCKs.
5. Handle loading queue, progress UI, error recovery.
6. Add client-side caching.

## Resources
- Godot Web Export Docs: Focus on HTTP restrictions, runtime loading.
- Colyseus Godot SDK: https://docs.colyseus.io/
- Lazy Loading Examples: Godot forums for PCK downloads.

## Best Practices
- Bundle assets by region/scene.
- Use server compression + client decompression.
- Monitor bandwidth; implement priority queuing.
- Test with serve.py from Godot repo initially.
