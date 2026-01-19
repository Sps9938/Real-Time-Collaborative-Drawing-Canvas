# Real-Time Collaborative Drawing Canvas

A multi-user drawing app where people sketch on the same canvas with real-time synchronization. The frontend lives in a submodule named `Frontend` pointing to `https://github.com/Sps9938/Real-Time-Collaborative-Drawing-Canvas-Frontend`.

## Repository structure
- Frontend/ — React + Vite client and Node/Socket.io server (submodule)
- .gitmodules — submodule configuration

## Clone and setup
Clone with the submodule in one go:
```bash
# SSH
# git clone --recurse-submodules git@github.com:Sps9938/Real-Time-Collaborative-Drawing-Canvas.git

# HTTPS
git clone --recurse-submodules https://github.com/Sps9938/Real-Time-Collaborative-Drawing-Canvas.git
cd Real-Time-Collaborative-Drawing-Canvas
```
If you already cloned without `--recurse-submodules`:
```bash
git submodule update --init --recursive
```

## Run the app (frontend+server)
From the repo root:
```bash
cd Frontend
npm install
npm run dev
```
That runs both the websocket server and the Vite client (via `concurrently`).

## Deploying: frontend on Vercel, backend on Render
- Frontend (Vercel): build from `Frontend` using `npm install && npm run build`; set `VITE_SERVER_URL` to your Render backend URL.
- Backend (Render Web Service):
	- Root Directory: `Server`
	- Build Command: `npm install`
	- Start Command: `npm run server` (or `node server/server.js`)
	- Env vars: `CLIENT_ORIGIN=https://your-vercel-frontend-domain`, `NODE_ENV=production` (optional). Render provides `PORT` automatically; do not override.
	- This avoids the build error `Could not resolve "/opt/render/project/src/client/vite.config.js"`, which happens when Render tries to build from the repo root instead of `Server`.

Once deployed, redeploy Vercel so the client picks up the correct `VITE_SERVER_URL` and sockets connect.

## What the project delivers
- Real-time drawing with brushes, eraser, colors, and stroke widths
- Live cursor indicators and online user list
- Global undo/redo across users
- Conflict handling when strokes overlap
- Socket.io-based event streaming for low-latency sync

## Tech stack
- React + Vite + Tailwind for the client
- Node.js + Express + Socket.io for realtime transport
- UUID for user/session IDs

## Submodule tips
- To pull latest frontend changes: `cd Frontend && git pull`
- To update the submodule pointer after pulling: `git submodule update --remote Frontend`
- If the submodule URL ever changes, edit `.gitmodules`, then run `git submodule sync --recursive`
