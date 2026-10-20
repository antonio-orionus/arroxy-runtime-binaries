# Arroxy Runtime Binaries — agent guide

This repository publishes the signed runtime manifests the Arroxy desktop app trusts, and hosts the FFmpeg package releases. `README.md` explains the releases and the publishing workflow; read it first. Arroxy's `AGENTS.md` ("Related repositories") maps every Arroxy repository.

## Most changes belong in Arroxy

This repo has no source of its own. It owns only `.github/workflows/publish-runtime-manifest.yml` and its docs.

- **Manifest contents** (yt-dlp sources, FFmpeg packages, download URLs) come from Arroxy's `scripts/build/runtimeBinaryManifest.ts`, run against Arroxy `main`. To change what users get, change [Arroxy](https://github.com/antonio-orionus/Arroxy); FFmpeg pins live in its `FfmpegPackagePins.ts`. Never edit a manifest here or in a release.
- **FFmpeg package releases** are published by hand from Arroxy, following its `dev-docs/ffmpeg-packages.md`.
- **The signing public key** lives in Arroxy's `src/main/services/binary/RuntimeBinaryTrust.ts` and in the workflow's `RUNTIME_INDEX_PUBLIC_KEY`; change both together.

## Releases

Whichever release GitHub calls _latest_ is the manifest every install trusts. After creating or editing any release by hand, check it:

```bash
gh api repos/antonio-orionus/arroxy-runtime-binaries/releases/latest --jq '.tag_name'   # must start with runtime-
```

## Changing the workflow

Workflow changes go through a PR. Keep the job split and its least-privilege permissions described in `README.md`: Arroxy code runs only in `generate`, which has a read-only token and no secrets, and only `publish` can write releases. Pin every third-party action to a full commit SHA.
