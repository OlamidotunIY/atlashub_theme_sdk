# Engineering guidelines and security rules for SDK, CLI and generator

**Normative; source not yet implemented.**

## Language/tooling
TypeScript strict mode, explicit public exported APIs, pinned toolchain and Node LTS supported matrix; React+Vite+React Router for generated themes. Format/lint/typecheck as CI gates. No `any` for public SDK types, no unvalidated API JSON, no unchecked `! ` assertions on external data.

## Dependency boundaries
- Domain policies import pure types/value objects only. Application uses ports injected via constructors/factories. Adapters implement ports; CLI/hook entrypoints perform translation but no business/security authority.
- Never import Java backend source directly; use generated typed public facade/management API contracts with explicit versions. Backend validates authorization, tenant and payments.
- React SDK hooks are adapters around public query/client use cases, not place to hide merchant entitlements, price changes or permission enforcement.
- No direct cross-repo imports, no Turborepo/Nx, no duplication of Auth Web login flows in theme generator. CLI device/browser login uses central Auth Web + IdP.

## Theme validation and capabilities
Manifest, creator/version, page contract, routes, asset references, locale/market, SSR compatibility and settings defaults validated before build. Allow custom styles, components, animations and permitted libraries. Restrict dangerous arbitrary server modules, runtime code generation, external network endpoints and unsupported Node APIs in edge SSR. Set explicit security review rules, rather than assuming React itself is safe.

## Secrets, browser and storage
- Do not embed merchant/provider keys, Auth Web or portal tokens or elevated Java credentials in `dist`, source archive or sample theme.
- CLI scoped credential storage and revocation, never committed; short-lived signed upload targets from server; never log raw credentials.
- Developer source archive is private; Storefront backend independently rebuilds in disposable isolated workers and reviews before listing. CLI status `SUBMITTED` != `APPROVED`/public.
- Merchant asset IDs and overrides are handled by storefront backend; developer defaults must be valid.
- Browser runs untrusted theme JS. Allowlisted SDK methods do not prevent browser exfiltration; enforce CSP/egress review and trusted external checkout. Never promise security solely due to SDK.
- Signed/verified hostname->store context comes from Storefront hosting/gateway; no browser-editable org ID authorization.
- Versioned releases never modified in place, preserve merchant override compatibility and rollback.

## API and errors
Return typed results or stable typed errors with retryability; preserve aborted requests and timeouts. No swallowing failed publish/build/payment attempts or fabricating successful SDK responses. All mutations get stable idempotency keys where backend specifies.
Public APIs use explicit `@since`/deprecations and semver compatibility; upgrade matrix maintained with backend/hosting version.

## Code reviews
Each PR must specify bounded context, invariants, API surface changes, dependency direction, security impact, migration/compatibility, tests and docs updates. Never merge source by editing examples only without verifying backend contracts. Feature flags for experimental SDK capabilities.
