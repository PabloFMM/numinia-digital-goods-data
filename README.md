# Numinia Digital Goods — Data Repository

The open data layer for [numinia.store](https://numinia.store). All digital assets (3D models, avatars, worlds, audio, video) and their metadata live here.

**Philosophy: File Over App** -- this repo IS the source of truth. The web app is just a viewer. If numinia.store disappears, the data remains here, on IPFS, and on Arweave. Fork it, build your own viewer, use it however you want.

**License: CC0** -- all assets are public domain. No attribution required.

## What's inside

```
numinia-digital-goods-data/
├── content/                    Binary files (the actual assets)
│   ├── avatars/                VRM avatars (6 files)
│   ├── models/                 GLB 3D models (4 files)
│   ├── worlds/                 HYP Hyperfy worlds (2 files)
│   ├── audio/                  MP3/OGG sound files
│   ├── video/                  MP4/WebM video files
│   └── thumbnails/             PNG preview images
│
├── data/                       JSON metadata (what the app reads)
│   ├── projects.json           Project index (5 categories)
│   ├── avatars/                VRM avatar catalog
│   ├── assets/                 GLB model catalog
│   ├── worlds/                 HYP world catalog
│   ├── audio/                  Audio catalog
│   ├── video/                  Video catalog
│   └── versions/               Version history per asset
│
└── download-counts.json        Download tracking
```

## Current assets

| Type | Count | Format | Folder |
|---|---|---|---|
| Avatars | 7 | VRM | `content/avatars/` |
| 3D Models | 5 | GLB | `content/models/` |
| Worlds | 3 | HYP | `content/worlds/` |
| Audio | 2 | MP3 | `content/audio/` |
| Video | 2 | MP4 | `content/video/` |

## Asset ID system

Every asset has a unique ID following the [ndg protocol](https://github.com/PabloFMM/numinia-digital-goods/blob/main/ID_SYSTEM.md):

```
ndg-019d3ded-a1dd-7e0e-aeff-cb155bdff3d8
 |   |___________________________________|
 |              UUID v7 (RFC 9562)
 |
 ndg = Numinia Digital Goods namespace
```

- **UUID v7**: timestamp-sortable, 122 bits entropy, zero collisions
- **Backward compatible**: legacy IDs (slug-based) still work
- **Canonical form**: `ndg:vrm:019d3ded-...:v0.1.0` (for NFT tokenURIs, exports)

## JSON schema

Each asset entry contains:

```json
{
  "id": "ndg-019d3ded-a1dd-7e0e-aeff-cb155bdff3d8",
  "canonical": "ndg:vrm:019d3ded-...:v0.1.0",
  "name": "Avatar Lyra 0.1.3",
  "type": "vrm",
  "version": "0.1.0",
  "status": "active",
  "license": "CC0",
  "creator": "PabloFMM",

  "model_file_url": "https://pub-eda9...r2.dev/content/avatars/ndg-019d3ded-....vrm",
  "thumbnail_url": "https://pub-eda9...r2.dev/content/thumbnails/ndg-019d3ded-....png",
  "format": "VRM",
  "content_type": "model/gltf-binary",
  "file_size_bytes": 2608148,
  "file_hash": null,

  "nft": {
    "type": "standard",
    "mint_status": "unminted",
    "chain_id": null,
    "contract": null,
    "token_id": null
  },

  "storage": {
    "r2": "https://pub-eda9...r2.dev/...",
    "ipfs_cid": null,
    "arweave_tx": null,
    "github_raw": "https://raw.githubusercontent.com/..."
  },

  "is_public": true,
  "created_at": "2026-03-30T...",
  "updated_at": "2026-03-30T..."
}
```

### Status values
- `active` -- visible and downloadable
- `deprecated` -- old version, still downloadable
- `stolen` -- flagged, red badge (File Over App: still downloadable)
- `cursed` -- game mechanic flag
- `frozen` -- legal hold

### NFT support
Assets can be linked to on-chain NFTs (ERC-721/1155). The `nft` field tracks:
- `mint_status`: unminted / pending / minted
- `chain_id`: blockchain (e.g., Base = 8453)
- `contract` + `token_id`: on-chain reference
- `access_type`: "original" (signed copy), "exclusive" (owner-only), "access_key" (gating)

## Storage layers

Assets are stored redundantly across multiple layers:

| Layer | Purpose | Status |
|---|---|---|
| **Cloudflare R2** | CDN, fast delivery | Active |
| **GitHub** | Metadata JSON, small binaries | Active |
| **IPFS** | Decentralized distribution | Planned |
| **Arweave** | Permanent archival | Planned |
| **Amazon S3** | Backup | Planned |

Each asset's `storage` object tracks which layers have a copy.

## How to consume this data

### From code (fetch JSON)
```javascript
const res = await fetch(
  'https://raw.githubusercontent.com/PabloFMM/numinia-digital-goods-data/main/data/avatars/numinia-avatars.json'
);
const avatars = await res.json();
```

### From the API
```
GET https://numinia.store/api/assets
GET https://numinia.store/api/assets?search=avatar
GET https://numinia.store/api/collections
```

### Download a file directly
```
https://raw.githubusercontent.com/PabloFMM/numinia-digital-goods-data/main/content/avatars/starter-avatar-01.vrm
```

Or from R2 (faster):
```
https://pub-eda9f42266254c179ab493dd1594bb5a.r2.dev/content/avatars/ndg-019d3ded-....vrm
```

## Contributing

### Add assets via admin panel
Go to [numinia.store/en/admin](https://numinia.store/en/admin), connect with MetaMask, drag & drop files.

### Add assets via PR
1. Put binary in `content/{type}/`
2. Add JSON entry to `data/{type}/numinia-{type}.json`
3. Follow the schema above
4. Submit PR

### Supported formats
- **3D Models**: GLB, VRM
- **Worlds**: HYP (Hyperfy)
- **Audio**: MP3, OGG
- **Video**: MP4, WebM

## Related repositories

| Repo | Purpose |
|---|---|
| [numinia-digital-goods](https://github.com/PabloFMM/numinia-digital-goods) | Next.js app (the viewer/admin) |
| [numinia-digital-goods-data](https://github.com/PabloFMM/numinia-digital-goods-data) | This repo (data + assets) |

## Links

- [numinia.store](https://numinia.store)
- [@numinia_store](https://x.com/numinia_store)
- [numen.games](https://numen.games)
- [ID System Specification](https://github.com/PabloFMM/numinia-digital-goods/blob/main/ID_SYSTEM.md)
- [Storage Strategy](https://github.com/PabloFMM/numinia-digital-goods/blob/main/STORAGE_STRATEGY.md)
