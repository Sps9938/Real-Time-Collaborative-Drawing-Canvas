# Real-Time Collaborative Drawing Canvas

Real-time multi-user whiteboard that streams strokes, cursors, and undo/redo events over Socket.IO. This repo tracks two submodules: `Frontend` (Vite/React client) and `Server` (Node.js Socket.IO backend).

## Repo layout
- Frontend/ — Vite + React client (includes a lightweight dev server inside the submodule)
- Server/ — Node.js + Express + Socket.IO backend (authoritative stroke + presence store)
- ARCHITECTURE.md — high-level system design
- .gitmodules — submodule configuration

## Clone with submodules
```bash
git clone --recurse-submodules https://github.com/Sps9938/Real-Time-Collaborative-Drawing-Canvas.git
cd Real-Time-Collaborative-Drawing-Canvas
# if already cloned: git submodule update --init --recursive
```

## Run locally
1) Install dependencies
```bash
cd Server && npm install
cd ../Frontend && npm install
```
2) Start the backend
```bash
cd ../Server
PORT=3001 CLIENT_ORIGIN=http://localhost:5173 npm run dev
```
3) Start the frontend (pointing at the backend above)
```bash
cd ../Frontend
VITE_SERVER_URL=http://localhost:3001 npm run client
# for a one-command demo that also boots the submodule's bundled server: npm run dev
```
Then open http://localhost:5173.

## Deploy
- Frontend (Vercel/Netlify/S3): build from `Frontend` with `npm run build`; set `VITE_SERVER_URL` to your backend URL.
- Backend (Render/Fly/Heroku): deploy from `Server`; host sets `PORT`, and `CLIENT_ORIGIN` should match your frontend origin. If you ship the built client with the backend, copy `Frontend/dist` into `Server/dist` and run with `NODE_ENV=production` to serve static assets.
- After changing the backend URL, redeploy the frontend so the baked `VITE_SERVER_URL` stays in sync.

## Features
- Real-time stroke streaming with brush/eraser, colors, and stroke widths
- Presence: live cursors plus join/leave notifications and names/colors
- Global undo/redo with ordered stroke history and last-writer-wins rendering
- Normalized coordinates so canvases stay consistent across screen sizes

## Submodule upkeep
- Pull latest pointers: `git submodule update --remote Frontend Server`
- If URLs change, edit `.gitmodules` and run `git submodule sync --recursive`
