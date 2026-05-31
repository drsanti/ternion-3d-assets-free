# ternion-3d-assets-free

Official **remote asset feed** for [TRN Node Animator](https://github.com/drsanti/node-animator), [Bitstream Studio](https://github.com/drsanti/Bitstream-Studio), and other Ternion tools.

- **Repository:** https://github.com/drsanti/ternion-3d-assets-free  
- **Canonical folder:** [`assets/`](assets/) on branch **`main`** — **all new content goes here**  
- **Layout reference:** [`docs/LAYOUT.md`](docs/LAYOUT.md)

---

## Canonical URLs (do not confuse repo root with `assets/`)

| Consumer | What to use |
|----------|-------------|
| **Browse files** | https://github.com/drsanti/ternion-3d-assets-free/tree/main/assets |
| **Bitstream Studio** (`ONLINE_ASSETS_BASE_URI`) | `https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main/assets` |
| **Node Animator feed** | `https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main/assets/feed.json` |
| **Sensor Studio manifest** | `https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main/assets/studio-asset-manifest.v1.json` |

**Bitstream** joins `{online base}` + `{relativePath}` where paths look like `models/psoc-e84-ai/psoc-e84-ai.glb` (no `assets/` prefix).

**Node Animator** reads [`assets/feed.json`](assets/feed.json). The feed’s `baseUrl` is the **repository root** (`.../main`); each library `manifest` is repo-relative (e.g. `assets/models/manifest.json`).

**Do not** publish duplicate trees at the repository root. Legacy root `models/`, `textures/`, and root `studio-asset-manifest.v1.json` were removed **2026-05-31**.

---

## Quick start

### Node Animator

```bash
cd node-animator
npm run download:feeds
```

### Bitstream Studio

Free Loader sync pulls every blob under GitHub `assets/` into local `globalStorage/.../assets/free/`. Online fallback uses `.../main/assets` — see Bitstream doc `extension/docs/ASSETS_ONLINE_REPO.md`.

---

## Repository layout

```text
ternion-3d-assets-free/
├── README.md
├── docs/
│   └── LAYOUT.md
└── assets/                       ← publish here only
    ├── feed.json                 # bump revision on every publish
    ├── studio-asset-manifest.v1.json
    ├── models/
    │   ├── manifest.json
    │   └── <model-id>/<model-id>.glb
    ├── textures/
    │   ├── cubemap/
    │   ├── images/
    │   └── hdri/
    └── libraries/
        └── node-graph/
```

---

## Before you start

### Step 0 — Clone this repo

```bash
git clone https://github.com/drsanti/ternion-3d-assets-free.git
cd ternion-3d-assets-free
```

### Step 0b — Clone Node Animator (to test sync)

```bash
git clone https://github.com/drsanti/node-animator.git
cd node-animator
npm install
```

---

## Publishing checklist (every change)

Use this checklist **every time** you add or update assets:

| Step | Action |
|------|--------|
| 1 | Add or update files under **`assets/`** only (see sections below) |
| 2 | Update the relevant **manifest** or **index.json** |
| 3 | **Bump `revision`** in [`assets/feed.json`](assets/feed.json) (new ISO-8601 timestamp) |
| 4 | `git add` → `git commit` → `git push` to **`main`** |
| 5 | In **node-animator**, run `npm run download:feeds` and verify in **Asset Browser** |
| 6 | Optional: Bitstream — Free Loader sync or Asset Manager connection test |

**Example revision bump:**

```json
"revision": "2026-05-31T08:00:00Z"
```

---

## Step 1 — Publish a 3D model (GLB)

### 1.1 Add the file

```text
assets/models/<model-id>/<model-id>.glb
```

**Example:**

```text
assets/models/robot-arm/robot-arm.glb
```

### 1.2 Update the models manifest

Edit [`assets/models/manifest.json`](assets/models/manifest.json) — JSON array of folder names:

```json
[
  "abb-robot-arm",
  "robot-arm",
  "your-new-model-id"
]
```

**Optional — regenerate from disk:**

```bash
node -e "
const fs = require('fs');
const path = require('path');
const dir = 'assets/models';
const names = fs.readdirSync(dir)
  .filter((n) => fs.statSync(path.join(dir, n)).isDirectory());
fs.writeFileSync(path.join(dir, 'manifest.json'), JSON.stringify(names, null, 2));
console.log('models:', names.length);
"
```

### 1.3 Bump feed revision, commit, push

See [Publishing checklist](#publishing-checklist-every-change).

### 1.4 Verify in Node Animator

```bash
cd node-animator
npm run download:feeds
npm run dev
```

Open **Asset Browser** → **Scene** → drag the model onto the Brain.

---

## Step 2 — Publish a cubemap (environment)

### 2.1 Add face images

```text
assets/textures/cubemap/<cubemap-id>/
```

Typical face names: `px.jpg`, `nx.jpg`, `py.jpg`, `ny.jpg`, `pz.jpg`, `nz.jpg` (or `posx.jpg`, …).

### 2.2 Update the cubemap manifest

Edit [`assets/textures/cubemap/manifest.json`](assets/textures/cubemap/manifest.json):

```json
[
  "Yokohama3",
  "your-new-cubemap-id"
]
```

### 2.3 Bump feed revision, commit, push

See [Publishing checklist](#publishing-checklist-every-change).

### 2.4 Verify

```bash
cd node-animator
npm run download:feeds
```

---

## Step 3 — Publish a node graph preset

### 3.1 Create the preset in Node Animator

1. Open **node-animator** → `npm run dev`
2. Build a **Node Group** on the Brain canvas
3. **Inspector** → **Export** → save `your-preset.trn-node-asset.json`

### 3.2 Validate (recommended)

```bash
npx tsx scripts/validate-node-asset.ts path/to/your-preset.trn-node-asset.json
```

### 3.3 Copy into this repo

```text
assets/libraries/node-graph/<preset-id>.trn-node-asset.json
```

### 3.4 Add a catalogue entry

Edit [`assets/libraries/node-graph/index.json`](assets/libraries/node-graph/index.json).

**Categories:** `animation` | `data` | `scene` | `math` | `utility` | `composition`

### 3.5 Bump feed revision, commit, push

See [Publishing checklist](#publishing-checklist-every-change).

---

## Step 4 — Sensor Studio manifest (Bitstream Studio)

Curated Asset Browser rows live in [`assets/studio-asset-manifest.v1.json`](assets/studio-asset-manifest.v1.json). Paths in that file are **relative to `.../main/assets`** (same as Bitstream `relativePath`).

Publish from Bitstream Studio:

```bash
cd extension
export GITHUB_TOKEN=ghp_...
npm run publish:studio-asset-manifest
```

---

## Step 5 — Pull updates on another machine

```bash
cd node-animator
git pull
npm run download:feeds
npm run dev
```

Or use **refresh** in Asset Browser while `npm run dev` is running.

---

## Feed manifest reference

[`assets/feed.json`](assets/feed.json) points sync clients at child manifests:

| Library key | Manifest path | Local cache (node-animator) |
|-------------|---------------|-----------------------------|
| `scene` | `assets/models/manifest.json` | `public/assets/models/` |
| `texture.cubemap` | `assets/textures/cubemap/manifest.json` | `public/assets/textures/cubemap/` |
| `texture.images` | `assets/textures/images/manifest.json` | `public/assets/textures/images/` |
| `nodeGraph` | `assets/libraries/node-graph/index.json` | `public/assets/libraries/node-graph/` |

- **`baseUrl`** in the feed = repository root (`.../main`) — manifest paths include the `assets/` prefix.  
- Do not change `baseUrl` unless the repo moves. Always bump **`revision`** when any child asset changes.

---

## Troubleshooting

| Problem | What to do |
|---------|------------|
| New model not in Asset Browser | `assets/models/<id>/<id>.glb` exists; manifest lists `<id>`; feed `revision` bumped; `npm run download:feeds` |
| Bitstream 404 on GLB | URL must be `.../main/assets/models/...`, not `.../main/models/...` |
| Node preset not listed | Check `index.json` + file name; validate JSON; bump `revision` |
| Sync shows stale files | Delete local cache folder and re-sync |
| `feed.json` 404 | Pushed to **`main`**; URL is `.../main/assets/feed.json` |

---

## Related documentation

| Doc | Location |
|-----|----------|
| Layout + consumer URLs | [`docs/LAYOUT.md`](docs/LAYOUT.md) |
| Node Animator feeds | [asset-feeds-and-libraries.md](https://github.com/drsanti/node-animator/blob/main/docs/guides/asset-feeds-and-libraries.md) |
| Bitstream online assets | [ASSETS_ONLINE_REPO.md](https://github.com/drsanti/Bitstream-Studio/blob/main/extension/docs/ASSETS_ONLINE_REPO.md) |

---

## Legacy: Next.js / @ternion/t3d

Copy the **`assets/`** folder into a Next.js `public/assets` directory for the T3D engine, or prefer **Node Animator** + `npm run download:feeds` for new work.

For new work, prefer **Node Animator** or **Bitstream Studio Free Loader sync** instead of manual root-level copies.
