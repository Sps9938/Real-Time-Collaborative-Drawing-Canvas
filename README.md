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
