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

## Setup and run locally
Backend (Server submodule)
- Install and start: `cd Server && npm install && PORT=3001 CLIENT_ORIGIN=http://localhost:5173 npm start`

Frontend (Frontend submodule)
- Install: `cd Frontend && npm install`
- Start client pointing to the backend: `VITE_SERVER_URL=http://localhost:3001 npm run client`
- One-command demo (runs bundled server + client together): `npm run dev`

Then open http://localhost:5173. If the backend port/origin differs, update `VITE_SERVER_URL` and `CLIENT_ORIGIN` accordingly.

## Test with multiple users
- Open the app in two browser tabs or devices pointed at the same server URL.
- Draw in one tab; strokes, cursors, and undo/redo should mirror in the other immediately.

## Deploy
- Frontend (Vercel/Netlify/S3): build from `Frontend` with `npm run build`; set `VITE_SERVER_URL` to your backend URL.
- Backend (Render/Fly/Heroku): deploy from `Server`; host sets `PORT`, and `CLIENT_ORIGIN` should match your frontend origin. If you ship the built client with the backend, copy `Frontend/dist` into `Server/dist` and run with `NODE_ENV=production` to serve static assets.
- After changing the backend URL, redeploy the frontend so the baked `VITE_SERVER_URL` stays in sync.

## Features
- Real-time stroke streaming with brush/eraser, colors, and stroke widths
- Presence: live cursors plus join/leave notifications and names/colors
- Global undo/redo with ordered stroke history and last-writer-wins rendering
- Normalized coordinates so canvases stay consistent across screen sizes

## Demo (video)
- Explanation and walkthrough: [Watch on Google Drive](https://drive.google.com/file/d/1S-QlwkCx6pGZV5wf9R5jVWoJgFhTPjnL/view?usp=drive_link)

## Live app
- Try it here: [Open the deployed canvas](https://real-time-collaborative-drawing-can-rho.vercel.app/)

## Contact
- Email: satyaprakash.8455995130@gmail.com

## Known limitations / bugs
- In-memory state: strokes and presence reset when the server restarts.
- Undo/redo operates per committed stroke, not per-segment.
- No authentication; names are ephemeral and not reserved.
- Scaling to multiple server instances requires a shared Socket.IO adapter (e.g., Redis) for consistent broadcasts.

## Time spent
- Total effort: 4 days

## Submodule upkeep
- Pull latest pointers: `git submodule update --remote Frontend Server`
- If URLs change, edit `.gitmodules` and run `git submodule sync --recursive`
