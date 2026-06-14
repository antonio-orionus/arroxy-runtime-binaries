# Arroxy Runtime Binaries

This repository is the machine-facing runtime manifest channel for Arroxy.

Arroxy desktop builds fetch the latest signed runtime metadata from:

```text
https://github.com/antonio-orionus/arroxy-runtime-binaries/releases/latest/download/runtime-index-v1.json
https://github.com/antonio-orionus/arroxy-runtime-binaries/releases/latest/download/runtime-index-v1.sig
```

Each release contains exactly:

- `runtime-index-v1.json`
- `runtime-index-v1.sig`

The manifest lists approved upstream or mirror URLs for runtime-managed binaries. The binaries themselves are not hosted here. Arroxy verifies `runtime-index-v1.sig` with the public key bundled in the app before trusting the manifest.

## Publishing

The `Publish Runtime Manifest` workflow runs on a schedule and can also be started manually. It checks out `antonio-orionus/Arroxy` read-only, runs Arroxy's runtime manifest generator, signs the manifest, validates the signature and current-host materialization, creates a draft immutable release, verifies both assets exist, then publishes the release as the final step.

Required repository secret:

- `ARROXY_RUNTIME_INDEX_SIGNING_KEY`

Immutable releases should remain enabled for this repository.
