# Repository layout and consumer URLs

**Canonical rule:** all publishable assets live under **`assets/`** on branch **`main`**. Do not add new files at the repository root.

## URLs

| Role | URL |
|------|-----|
| Browse on GitHub | https://github.com/drsanti/ternion-3d-assets-free/tree/main/assets |
| **Bitstream Studio** online base (`ONLINE_ASSETS_BASE_URI`) | https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main/assets |
| **Node Animator** feed entry | https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main/assets/feed.json |
| Feed `baseUrl` (repo root for manifest paths) | https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main |
| Sensor Studio manifest | https://raw.githubusercontent.com/drsanti/ternion-3d-assets-free/main/assets/studio-asset-manifest.v1.json |

## Tree

```text
ternion-3d-assets-free/
├── README.md
├── docs/
│   └── LAYOUT.md                 ← this file
└── assets/                       ← ONLINE ROOT
    ├── feed.json                 ← Node Animator sync (bump revision on every publish)
    ├── studio-asset-manifest.v1.json
    ├── feeds/
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

### Sensor Studio — MediaPipe vision pack (Bitstream)

```text
assets/vision/mediapipe/
  manifest.v1.json
  wasm/
  *.task / *.tflite
```

- Local after Free Loader: `globalStorage/.../assets/free/vision/mediapipe/...`
- Bitstream relative path: `vision/mediapipe/...` (no `assets/` prefix in manifest keys)
- Maintainer staging: Bitstream `npm run vision:stage-free-pack` → commit in this repo
- See Bitstream **`extension/docs/VISION_MEDIAPIPE_FREE_PACK.md`**

## How consumers resolve paths

### Bitstream Studio

Uses **`.../main/assets`** as base. Paths in `studio-asset-manifest.v1.json` are **relative to that base** (no `assets/` prefix):

```text
models/psoc-e84-ai/psoc-e84-ai.glb
→ .../main/assets/models/psoc-e84-ai/psoc-e84-ai.glb
```

Free Loader sync downloads every Git blob under `assets/` and strips the `assets/` prefix when writing to local `free/`.

### Node Animator

Reads **`assets/feed.json`**. The feed’s **`baseUrl`** is the **repository root** (`.../main`). Each library **`manifest`** path is repo-relative, e.g. `assets/models/manifest.json`:

```text
baseUrl + manifest
→ .../main/assets/models/manifest.json
```

## Removed legacy layout (2026-05-31)

These **root-level** paths were duplicates and have been **deleted**:

- `models/` (merged `robot-4w` into `assets/models/` first)
- `textures/`
- `studio-asset-manifest.v1.json` (use `assets/studio-asset-manifest.v1.json` only)

Do not restore them.

## Related

- Bitstream Studio maintainer doc: `extension/docs/ASSETS_ONLINE_REPO.md` in [Bitstream-Studio](https://github.com/drsanti/Bitstream-Studio)
- Node Animator: `docs/guides/asset-feeds-and-libraries.md`
