# ternion-3d-assets-free

Official **remote asset feed** for [TRN Node Animator](https://github.com/drsanti/node-animator) and other Ternion tools.

- **Repository:** https://github.com/drsanti/ternion-3d-assets-free  
- **Raw base URL (main):** `https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main`  
- **Feed manifest:** [`assets/feed.json`](assets/feed.json)

Consumers download assets with:

```bash
cd node-animator
npm run download:feeds
```

---

## Repository layout

```
ternion-3d-assets-free/
├── assets/
│   ├── feed.json                          # Root feed manifest (bump revision on every publish)
│   ├── models/
│   │   ├── manifest.json                  # List of model folder names
│   │   └── <model-id>/
│   │       └── <model-id>.glb
│   ├── textures/
│   │   └── cubemap/
│   │       ├── manifest.json              # List of cubemap folder names
│   │       └── <cubemap-id>/              # Six face images (px, nx, py, …)
│   └── libraries/
│       └── node-graph/
│           ├── index.json                 # Library catalogue
│           └── *.trn-node-asset.json      # Node group presets
└── README.md
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
| 1 | Add or update files under `assets/` (see sections below) |
| 2 | Update the relevant **manifest** or **index.json** |
| 3 | **Bump `revision`** in [`assets/feed.json`](assets/feed.json) (use a new ISO-8601 timestamp) |
| 4 | `git add` → `git commit` → `git push` to **`main`** |
| 5 | In **node-animator**, run `npm run download:feeds` and verify in **Asset Browser** |

**Example revision bump:**

```json
"revision": "2026-05-18T14:30:00Z"
```

---

## Step 1 — Publish a 3D model (GLB)

### 1.1 Add the file

Create a folder named after the model id and place the GLB inside:

```text
assets/models/<model-id>/<model-id>.glb
```

**Example:**

```text
assets/models/robot-arm/robot-arm.glb
```

### 1.2 Update the models manifest

Edit [`assets/models/manifest.json`](assets/models/manifest.json). It is a **JSON array of folder names** (legacy format, still supported by the sync script):

```json
[
  "abb-robot-arm",
  "robot-arm",
  "your-new-model-id"
]
```

Add your new `<model-id>` string to the array (keep alphabetical order if you like).

**Optional — regenerate the list from disk:**

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

Create a folder under `assets/textures/cubemap/` with six images (typical names: `px.jpg`, `nx.jpg`, `py.jpg`, `ny.jpg`, `pz.jpg`, `nz.jpg`).

```text
assets/textures/cubemap/<cubemap-id>/
```

### 2.2 Update the cubemap manifest

Edit [`assets/textures/cubemap/manifest.json`](assets/textures/cubemap/manifest.json) — same format as models: a **JSON array of folder names**:

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

Cubemaps appear in environment / HDRI-related nodes after sync (depending on app version).

---

## Step 3 — Publish a node graph preset

Node graph assets are **JSON presets** (saved Node Groups) used in the Node Animator **Asset Browser**.

### 3.1 Create the preset in Node Animator

1. Open **node-animator** → `npm run dev`
2. Build a **Node Group** on the Brain canvas
3. Select the group → **Inspector** → **Save to library** (optional, for local use)
4. **Inspector** → **Export** → save `your-preset.trn-node-asset.json`

### 3.2 Validate the file (recommended)

From the **node-animator** repo:

```bash
npx tsx scripts/validate-node-asset.ts path/to/your-preset.trn-node-asset.json
```

You should see: `OK: <name> (<id>)`

### 3.3 Copy into this repo

Copy the file here (use a stable id as the filename):

```text
assets/libraries/node-graph/<preset-id>.trn-node-asset.json
```

**Example (official sample):**

```text
assets/libraries/node-graph/trn-demo-float-out.trn-node-asset.json
```

The JSON must include:

- `"marker": "trn-node-asset"`
- `"version": 1`
- Exactly one `"nodeGroup"` in `"nodes"`
- Matching `"subgraphs"` entry

### 3.4 Add a catalogue entry

Edit [`assets/libraries/node-graph/index.json`](assets/libraries/node-graph/index.json):

```json
{
  "marker": "trn-library-index",
  "version": 1,
  "libraryId": "nodeGraph",
  "entries": [
    {
      "id": "your-preset-id",
      "name": "Human readable name",
      "category": "math",
      "file": "your-preset-id.trn-node-asset.json",
      "description": "Short description for the Asset Browser"
    }
  ]
}
```

**Categories:** `animation` | `data` | `scene` | `math` | `utility` | `composition`

Keep existing entries; append new ones.

### 3.5 Bump feed revision, commit, push

See [Publishing checklist](#publishing-checklist-every-change).

### 3.6 Verify in Node Animator

```bash
cd node-animator
npm run download:feeds
npm run dev
```

1. **Asset Browser** → tab **Node Graph**
2. Source: **TRN Official** (or synced cache)
3. Confirm your preset appears (official sample: **Demo Float Output** / `trn-demo-float-out`)
4. Drag onto the Brain — the group should instantiate

---

## Step 4 — Pull updates on another machine

Anyone using Node Animator:

```bash
cd node-animator
git pull
npm run download:feeds
npm run dev
```

Or use the **refresh** button in Asset Browser while `npm run dev` is running (dev server only).

---

## Feed manifest reference

[`assets/feed.json`](assets/feed.json) points sync clients at child manifests:

| Library key | Manifest path | Local cache folder (in node-animator) |
|-------------|---------------|----------------------------------------|
| `scene` | `assets/models/manifest.json` | `public/assets/models/` |
| `texture.cubemap` | `assets/textures/cubemap/manifest.json` | `public/assets/textures/cubemap/` |
| `nodeGraph` | `assets/libraries/node-graph/index.json` | `public/assets/libraries/node-graph/` |

Do not change `baseUrl` unless the repo moves. Always bump **`revision`** when any child asset changes.

---

## Troubleshooting

| Problem | What to do |
|---------|------------|
| New model not in Asset Browser | Check `assets/models/<id>/<id>.glb` exists; manifest lists `<id>`; feed `revision` bumped; run `npm run download:feeds` |
| Node preset not listed | Check `index.json` entry + file name; validate JSON; bump `revision`; re-sync |
| Sync still shows old files | Delete `node-animator/public/assets/models/<id>/` (or preset file) and run `npm run download:feeds` again |
| `feed.json` 404 | Ensure pushed to **`main`** branch; raw URL must match `baseUrl` in feed |
| Invalid preset | Run `npx tsx scripts/validate-node-asset.ts` in node-animator |

---

## Related documentation

In **node-animator**:

- [`docs/guides/asset-feeds-and-libraries.md`](https://github.com/drsanti/node-animator/blob/main/docs/guides/asset-feeds-and-libraries.md) — full user + maintainer guide  
- [`docs/architecture/16-asset-feeds-remote-sync.md`](https://github.com/drsanti/node-animator/blob/main/docs/architecture/16-asset-feeds-remote-sync.md) — architecture  

---

## Legacy: Next.js / @ternion/t3d

You can still copy the `assets` folder into a Next.js `public/assets` directory for the T3D engine.

### Install

```bash
npm install @ternion/t3d
```

### Example

```tsx
"use client";
import { useEffect } from "react";

const MODEL = "assets/models/scenes/dev_scenes.glb";

export default function Example() {
  useEffect(() => {
    const loadT3D = async () => {
      const { T3D } = await import("@ternion/t3d");
      const engine = new T3D();
      engine.loadModel(MODEL).then((model) => {
        engine.createBlenderScene(model);
      });
      return () => engine.dispose();
    };

    loadT3D();
  }, []);

  return (
    <div className="flex h-screen w-screen justify-center items-center bg-black text-white text-5xl">
      T3D Engine Example
    </div>
  );
}
```

For new work, prefer **Node Animator** + `npm run download:feeds` instead of manual copies.
