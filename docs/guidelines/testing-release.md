# Testing, release, CI and compatibility

**Status:** acceptance strategy; to implement in GitHub Actions as source is added.

## Test pyramid
1. **Unit**: settings schemas/defaults, path/route validation, semver compatibility, immutable release model, capability allowlist, filenames/archive traversal and domain value objects.
2. **Ports/contract**: fake vs HTTP Java public catalog/cart APIs, Storefront management upload/publish/status APIs; request/response JSON schemas, status errors, changing API versions.
3. **Integration**: CLI credentials, local filesystem scaffold, reproducible archive/lockfile, builder adapter (sandboxed), Vite+React Router server/client artifacts, SSR hydration and asset references.
4. **E2E**: scaffold new template -> local mocked catalog -> validate -> build -> upload to test Storefront server -> polling review -> preview -> install on two test stores with different data/defaults.
5. **Adversarial**: dependency lifecycle scripts, SSR Node-only import, malicious fetch, XSS, oversized asset/archive-bomb/path traversal, missing defaults, forbidden API, invalid token, cross-store context override.

## Required CI gates
Lint/format, TS `strict`/typecheck, test coverage thresholds based on policy, dependency/secret scanning, contract drift check against pinned OpenAPI/schema fixtures, demo theme SSR build and Node/browser package isolation. CI must not have production provider secrets or access to arbitrary merchant data; sandbox untrusted test builds.

## Package version policy
Version SDK, CLI, scaffolder, schema and runtime compatibility deliberately; can publish distinct packages from independent repo with standard scripts. Each release produces changelog, semver, lockfile/manifest fixture, generated TS declaration, browser/Node exports validated, integrity attestation and compatibility matrix.
- Patch: bug fix with unchanged public interface/manifest contract.
- Minor: optional additive API/schema features supported by backend capability negotiation.
- Major: breaking routes/schema/API or dropped runtime support; preserve migration guide.
- Backend and SDK versions may differ; reject unsupported manifests with actionable diagnostics rather than undefined behavior.

## Publish process
Review PR -> CI green -> release candidate against backend staging -> generate artifacts/provenance -> tag release -> publish npm package(s) using short-lived CI identities, not long-lived secrets -> update documentation and compatibility tests. Themes published through CLI still go through server quarantine/build/review.

## Performance and accessibility
Measure output size, SSR time and hydration quality against sample themes; add accessibility and SEO checks, type-safe error/loading states, failed-network recovery. Test Windows and Linux CLI filesystem paths, Node LTS matrix and supported browsers. Do not invent acceptance p95 numbers until prototype evaluation.

## Security and user data
Ensure SDK never sends customer/admin secrets to third-party scripts, never accesses private staff/billing endpoints, and does not override Java-authoritative pricing/stock. Tests include invalid provider capability, unknown payment state and secure checkout redirect origin.
