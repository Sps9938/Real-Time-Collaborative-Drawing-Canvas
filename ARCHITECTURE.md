# Architecture

## System view
- Vite/React client renders the canvas, captures pointer events, and streams strokes over Socket.IO.
- Node.js + Express + Socket.IO backend keeps the authoritative stroke history, presence, and rooms in memory.
- Data is transient (in-memory); persistence or horizontal scaling would require external state (Redis) and sticky sessions.

## Data and control flow
```
[Pointer/Touch]
   ↓
CanvasBoard (predictive draw)
   ↓ stroke events
Socket.IO client ───────────────→ WebSocket
                                  ↓
                       Socket.IO server (room registry + stroke store)
                                  ↓
                        broadcast strokes/cursors/undo/redo
                                  ↓
                           Other clients redraw
```
- Points are normalized to [0,1] so canvases of different sizes render identical geometry.
- Client draws optimistically while also replaying authoritative commits from the server to stay consistent.

## Event contract
- join `{ name?, room? }` → `init { user, users, strokes }` plus `user:joined` to peers.
- stroke:start `{ strokeId, tool, color, size }` → broadcast `stroke:start` with empty points.
- stroke:points `{ strokeId, points[] }` → broadcast `stroke:points` (normalized `{x,y}` points).
- stroke:end `{ strokeId }` → server commits; broadcast `stroke:commit { stroke }`.
- undo / redo → broadcast `stroke:undo { strokeId }` or `stroke:redo { stroke }`.
- clear → broadcast `clear` to wipe canvases.
- cursor:move `{ x, y }` → broadcast `cursor { userId, name, color, x, y }`.
- ping/pong for latency measurement.

## State and ordering
- Each room owns an ordered stroke list plus an undone stack; new commits clear redo.
- Eraser uses canvas `destination-out`, applied in the same order as brushes for deterministic results.
- Late or dropped cursor events do not harm consistency; the next update refreshes position.

## Performance and resilience
- Normalized coordinates reduce per-device pixel drift and simplify replay after resize.
- Incremental draws during pointer moves; full replay only on commit/undo/redo or reconnect.
- CORS origin is restricted via `CLIENT_ORIGIN`; server rejects events when user/room is missing.
- For multiple server instances, add a shared adapter (e.g., Redis) so Socket.IO broadcasts stay consistent.

## Deployment notes
- Frontend builds to static assets; backend can optionally serve `dist/` when `NODE_ENV=production`.
- Host-assigned `PORT` is respected; keep websockets on HTTPS/WSS in production.
- If you copy the built client into `Server/dist`, ensure cache headers allow quick re-hydration.

## Environment variables
- Frontend: `VITE_SERVER_URL` (websocket endpoint, default `http://localhost:3001`).
- Backend: `PORT` (default 3001), `CLIENT_ORIGIN` (allowed origin), `NODE_ENV` (enables static serving when `production`).

## Key implementation points
- Canvas input and optimistic drawing: [Frontend/client/components/CanvasBoard.jsx](Frontend/client/components/CanvasBoard.jsx), [Frontend/client/App.jsx](Frontend/client/App.jsx).
- WebSocket wiring: [Frontend/client/main.jsx](Frontend/client/main.jsx).
- Room and presence management: [Server/server/rooms.js](Server/server/rooms.js).
- Stroke lifecycle with undo/redo: [Server/server/drawing-state.js](Server/server/drawing-state.js).
- Socket server entrypoint: [Server/server/server.js](Server/server/server.js).
