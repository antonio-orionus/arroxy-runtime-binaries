# Arroxy Runtime Binaries

This repository is the machine-facing runtime manifest channel for Arroxy.

Arroxy desktop builds fetch the latest signed runtime metadata from:

```text
https://github.com/antonio-orionus/arroxy-runtime-binaries/releases/latest/download/runtime-index-v2.json
https://github.com/antonio-orionus/arroxy-runtime-binaries/releases/latest/download/runtime-index-v2.sig
```

Arroxy 0.6.2 and earlier read `runtime-index-v1.json` and `runtime-index-v1.sig` instead, so every runtime release contains exactly:

- `runtime-index-v1.json` (yt-dlp only)
- `runtime-index-v1.sig`
- `runtime-index-v2.json` (yt-dlp and the pinned FFmpeg packages)
- `runtime-index-v2.sig`

Each manifest lists approved download URLs for runtime-managed binaries. yt-dlp is not hosted here; the FFmpeg packages are, as `ffmpeg-*` releases. Arroxy verifies the `.sig` with the Ed25519 public key bundled in the app before trusting the matching manifest.

FFmpeg package releases must never become latest: create them with `--prerelease` and `--latest=false`. Every publish run also checks that `/releases/latest` serves the `runtime-*` release it just published.

## Publishing

The `Publish Runtime Manifest` workflow runs daily and can also be started manually. Its jobs are split by what they may touch:

1. `plan` resolves the Arroxy commit. Only a run of this repo's `main` against Arroxy `main` publishes; any other `arroxy_ref` or branch creates a draft release only.
2. `generate` runs Arroxy's generator and validator with a read-only token and no secrets, and requires both v1 and v2.
3. `sign` never runs Arroxy code. It checks each manifest's shape and that every download URL is on a known source, then signs with `openssl` and verifies against Arroxy's public key.
4. `publish` is the only job that can write. It creates a draft immutable release, checks its assets, publishes it as latest, then checks that `/releases/latest/download/` serves exactly these files with valid signatures.

Required repository secret:

- `ARROXY_RUNTIME_INDEX_SIGNING_KEY` (Ed25519 private key, PEM or base64-encoded PEM)

The workflow pins Arroxy's public key and fails if Arroxy's bundled key changes, so a key rotation means updating `RUNTIME_INDEX_PUBLIC_KEY` there too.

Immutable releases should remain enabled for this repository.
