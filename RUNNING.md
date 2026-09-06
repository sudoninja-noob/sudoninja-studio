# 🎛️ Sudo Ninja Studio — How to run

This project has **two parts** and both must be running:

| Part | What it is | Address (URL) |
|---|---|---|
| **Backend** | ACE-Step V1.5 (Python + GPU/MPS) — actually generates the music | `http://127.0.0.1:8001` |
| **Frontend** | React web UI (what you see in the browser) | `http://localhost:5173` |

> ⚠️ Running only the frontend won't make music — it needs a running backend.
> If the backend is down, the app shows an **"Offline"** pill and errors like
> `"Network error: Load failed"`.

---

## ✅ Easiest way — one command (backend + frontend together)

From the repo folder:

```bash
cd /Users/sanjaysingh/Documents/project/sudoninja-studio-main && ./start.sh
```

This automatically:
1. starts the backend and waits for `/health` to be ready
2. installs `frontend/` dependencies on first run
3. starts the frontend dev server at `http://localhost:5173`
4. streams both logs — press **`Ctrl-C`** to stop both cleanly

Then open in your browser: **http://localhost:5173**

---

## 🔧 Running them separately (when you want more control)

**Terminal 1 — Backend (macOS / Apple Silicon):**
```bash
cd /Users/sanjaysingh/Documents/project/sudoninja-studio-main && ACESTEP_INIT_LLM=true ./start_api_server_macos.sh
```
- `ACESTEP_INIT_LLM=true` is required so 5Hz LM features like **"Create sample" / "Enhance" / thinking** work.
- On Linux/CUDA: `./start_api_server.sh` · On Windows: `start_api_server.bat`

**Terminal 2 — Frontend:**
```bash
cd /Users/sanjaysingh/Documents/project/sudoninja-studio-main/frontend && npm run dev
```

---

## 🩺 Check whether it's running

```bash
curl -s http://127.0.0.1:8001/health
```
- Returns JSON (status, loaded_model…) → backend is ready ✅ (the app's **pill turns green**)
- `Couldn't connect` → backend isn't running yet

---

## ⏳ First run (one-time setup)

- The backend **downloads the ACE-Step model** on first run (several GB — needs internet and time). Be patient; later runs are faster.
- The frontend runs `npm install` on first run (`start.sh` does this for you).
- Required tools: **Node 18+**, **Python 3**, **uv** (all already installed).

---

## ⚙️ Handy env settings (optional)

With `start.sh`:
```bash
NINJA_BACKEND_PORT=8010 ./start.sh     # backend on a different port
NINJA_FRONTEND_PORT=3000 ./start.sh    # frontend on a different port
NINJA_SKIP_BACKEND=1 ./start.sh        # frontend only (backend already running)
NINJA_SKIP_FRONTEND=1 ./start.sh       # backend only
```

The frontend's backend address is set via `VITE_API_URL` in `frontend/.env.local`, or from the app's **Settings → ACE-Step API URL** (default `http://127.0.0.1:8001`).

---

## 🐛 Troubleshooting

| You see | Cause | Fix |
|---|---|---|
| `Network error: Load failed` / "Offline" pill | backend not running | Run the backend command above; wait for `/health` to go green |
| `Create sample failed` (even with backend up) | 5Hz LM not loaded | Start the backend with `ACESTEP_INIT_LLM=true` |
| `Port 8001 already in use` | an old backend is running | `lsof -ti :8001 \| xargs kill`, then start again |
| UI opens but generation does nothing | wrong URL in Settings | Set Settings URL to `http://127.0.0.1:8001` |

---

## 🛠️ Frontend dev commands (separately, if needed)

```bash
cd frontend
npm run dev         # dev server (HMR)
npm run typecheck   # TypeScript check
npm run lint        # ESLint
npm run build       # production build → dist/
npm run preview     # test the built bundle
```

---

_Made by Sudo Ninja Studio · Powered by ACE-Step V1.5_
