# app-switchyard-offline (private) &mdash; **SwitchYard.offline**

**A local, content-blind LLM router.** SwitchYard.offline is a Chrome MV3 extension that is
**nothing but a CPCP wrapper around NVIDIA SwitchYard's three no-inspection routing features**
&mdash; `passthrough`, `random`, `stage_router` &mdash; running **entirely on your device**.

It is the local remedy for the hosted [switchyard.online](https://switchyard.online) vulnerabilities
(`docs/VULNERABILITY_ANALYSIS.md`): your **provider credentials and prompts never leave your machine**.
SwitchYard.offline runs the inline proxy locally, stores per-provider keys in `chrome.storage.session`
only, and calls the upstream provider **directly** &mdash; so TLS is end-to-end (you &rarr; provider),
with no hosted endpoint decrypting your content.

## V1 scope (KISS)
- **3 strategies, content-blind:** passthrough, random (weighted), stage_router (progress-signal hints). **No LLM inspection of your prompts.**
- **No local LLM** for routing intelligence in V1. (A future V2 *could* use a local LLM to guide routing &mdash; foreshadowed, not committed.)
- **On-device credentials**, session-scoped; never transmitted off-device.
- **Narrow egress:** `host_permissions` + CSP allowlist the provider origins only. No arbitrary upstreams.
- **Same CPCP surface** as switchyard.online (CID-grounded `/_cpcp`, never-raise) &mdash; clients/threedot point at the local extension instead of the hosted service.

## Layout (app-browser-plugin model)
```
shared/   browser-agnostic core: router, routes, egress, contract, errors, cid.template.json
chrome/   MV3 overlay: manifest.json, service-worker.js (local CPCP surface), credential-store.js, popup
build/    generate-cid, build, check-manifest, generate-sbom, package, clean (-> dist/chrome)
tests/    router + manifest policy gates
docs/     DESIGN.md (Manus V1 design), VULNERABILITY_ANALYSIS.md (what this closes)
```

Upstream engine: **NVIDIA NeMo Switchyard** (pre-alpha) &mdash; github.com/NVIDIA-NeMo/Switchyard.
Design: `docs/DESIGN.md`. Private; LicenseRef-DataYoursSoftwareMine-1.0.
