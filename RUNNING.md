# 🎛️ Sudo Ninja Studio — कैसे चलाएँ (How to run)

इस project के **दो हिस्से** हैं और दोनों चलने चाहिए:

| हिस्सा | क्या | पता (URL) |
|---|---|---|
| **Backend** | ACE-Step V1.5 (Python + GPU/MPS) — असली music बनाता है | `http://127.0.0.1:8001` |
| **Frontend** | React वेब UI (जो आप ब्राउज़र में देखते हैं) | `http://localhost:5173` |

> ⚠️ अकेला frontend चलाने से music नहीं बनेगा — उसे चलता हुआ backend चाहिए।
> अगर backend बंद है तो app में pill **"Offline"** दिखता है और
> `"Network error: Load failed"` जैसी errors आती हैं।

---

## ✅ सबसे आसान तरीका — एक कमांड (backend + frontend दोनों)

repo फ़ोल्डर में:

```bash
cd /Users/sanjaysingh/Documents/project/sudoninja-studio-main && ./start.sh
```

यह अपने-आप:
1. backend शुरू करता है और `/health` के तैयार होने का इंतज़ार करता है
2. पहली बार `frontend/` की deps install करता है
3. frontend dev server `http://localhost:5173` पर चालू करता है
4. दोनों के logs दिखाता है — **`Ctrl-C`** से दोनों साफ़-सुथरे बंद हो जाते हैं

फिर ब्राउज़र में खोलें: **http://localhost:5173**

---

## 🔧 अलग-अलग चलाना (जब ज़्यादा control चाहिए)

**Terminal 1 — Backend (macOS / Apple Silicon):**
```bash
cd /Users/sanjaysingh/Documents/project/sudoninja-studio-main && ACESTEP_INIT_LLM=true ./start_api_server_macos.sh
```
- `ACESTEP_INIT_LLM=true` ज़रूरी है ताकि **"Create sample" / "Enhance" / thinking** जैसे 5Hz LM फ़ीचर्स चलें।
- Linux/CUDA पर: `./start_api_server.sh` · Windows पर: `start_api_server.bat`

**Terminal 2 — Frontend:**
```bash
cd /Users/sanjaysingh/Documents/project/sudoninja-studio-main/frontend && npm run dev
```

---

## 🩺 चल रहा है या नहीं — जाँचें

```bash
curl -s http://127.0.0.1:8001/health
```
- JSON (status, loaded_model…) आ जाए → backend तैयार ✅ (app में **pill हरा** हो जाएगा)
- `Couldn't connect` → backend अभी चालू नहीं

---

## ⏳ पहली बार चलाते समय (एक बार का सेटअप)

- Backend पहली बार **ACE-Step model download** करता है (कई GB — इंटरनेट/समय लगेगा)। धैर्य रखें; बाद में तेज़ चलता है।
- Frontend पहली बार `npm install` चलाता है (`start.sh` अपने-आप कर देता है)।
- ज़रूरी tools: **Node 18+**, **Python 3**, **uv** (सब पहले से installed हैं)।

---

## ⚙️ काम की env settings (वैकल्पिक)

`start.sh` के साथ:
```bash
NINJA_BACKEND_PORT=8010 ./start.sh     # backend दूसरे port पर
NINJA_FRONTEND_PORT=3000 ./start.sh    # frontend दूसरे port पर
NINJA_SKIP_BACKEND=1 ./start.sh        # सिर्फ़ frontend (backend पहले से चल रहा हो)
NINJA_SKIP_FRONTEND=1 ./start.sh       # सिर्फ़ backend
```

Frontend का backend पता `frontend/.env.local` में `VITE_API_URL` से या app के **Settings → ACE-Step API URL** से बदलता है (default `http://127.0.0.1:8001`)।

---

## 🐛 आम दिक्कतें (Troubleshooting)

| दिखता है | कारण | हल |
|---|---|---|
| `Network error: Load failed` / pill "Offline" | backend चालू नहीं | ऊपर वाली backend कमांड चलाएँ, `/health` हरा होने दें |
| `Create sample failed` (backend चालू है फिर भी) | 5Hz LM लोड नहीं | backend को `ACESTEP_INIT_LLM=true` के साथ चलाएँ |
| `Port 8001 already in use` | पुराना backend चल रहा है | `lsof -ti :8001 \| xargs kill` फिर दोबारा चलाएँ |
| UI खुलता है पर generate नहीं होता | Settings का URL गलत | Settings में URL = `http://127.0.0.1:8001` रखें |

---

## 🛠️ Frontend dev commands (अलग से, ज़रूरत पड़े तो)

```bash
cd frontend
npm run dev         # dev server (HMR)
npm run typecheck   # TypeScript जाँच
npm run lint        # ESLint
npm run build       # production build → dist/
npm run preview     # बने bundle को टेस्ट करना
```

---

_बना: Sudo Ninja Studio · Powered by ACE-Step V1.5_
