# Architecture — Domain-Driven Design, Clean Architecture, Hexagonal/Ports and Adapters

**Status:** Normative engineering policy for future source; only docs exist today.

## Core principle
**Dependencies point inward:** presentation/CLI/framework adapters → application use cases → domain policies and contracts. The domain **must not import** Vite, React, React Router, HTTP clients, Node process APIs, Cloudflare bindings, or the merchant management API implementation. Use TS interfaces/types and dependency injection at composition root.

**DDD applied pragmatically:** this is a *developer tooling/library* domain, not an OLTP business service. Useful bounded contexts include `ThemeDefinition`, `ThemeValidation`, `BuildArtifact`, `CreatorPublication`, `PublicStorefrontApi` and `DeveloperSession`. Value objects include ThemeId, SemVer, SDKCompatibility, PageType, SettingsSchema, Capability, ArtifactDigest. No artificial Aggregate/Repository for every hook or HTTP result.

### Core invariants
- A theme describes required pages/routes, valid configuration schema and defaults, required approved capabilities, a creator/version and compatible SDK/runtime.
- Settings defaults must be valid and usable before merchant overrides; optional merchant-specific IDs must have safe fallback behavior, never reference another merchant's data.
- Theme implementation may render and request *public* storefront data only. SDK may list/get public products/collections, cart operations and *initiate trusted* checkout; no private admin, payroll, ledger, owner credentials, direct merchant account data or provider settlement APIs.
- Namespaces/merchant context come from hosting platform's verified store mapping, **not developer-supplied organization identifiers**.
- A publish request uploads a candidate source version; it does not automatically become an approved marketplace release.
- Approved versions are immutable; compatibility policy and semantic versioning must be explicit.

## Hexagonal ports
- `PublicStorefrontGateway`: published product/collection/context, cart and trusted checkout session operations; implementations can be HTTP/fake fixture.
- `ThemeRegistryGateway`: creator-owned theme metadata, source upload reservation, job/review/version statuses; implementation HTTP backend.
- `CredentialStore`: short-lived CLI credentials, platform-secure OS vault implementation; not saved in theme project or browser bundle.
- `ArchiveBuilder`, `BuildExecutor`, `ArtifactHasher`, `FileAccess`, `Clock`, `Logger`: CLI/Node infrastructure adapters.
- `ThemeSchemaValidator`: enforce manifest/page/settings/capabilities, composable independent of React and network.

## Layers and import rules
- **Domain**: pure TS policies/value objects/schema contract definitions, no I/O/React/Node; tests without mocks.
- **Application**: `ValidateTheme`, `CreateThemeProject`, `BuildTheme`, `SubmitThemeVersion`, `GetBuildStatus`, `GetThemePreview`; use ports and typed outcomes; domain does not depend on use cases.
- **Infrastructure**: HTTP fetch, filesystem, archive, package manager, Vite/React Router, auth browser/device login, signing/digest, telemetry; adapt external errors into application types.
- **Presentation**: CLI commands/options/help, React SDK public hooks/provider, scaffolding templates; no business invariants hidden in CLI flag parsers or hooks.
- **Composition root** wires real port implementations. Local mock adapters must obey the same contracts as production clients.

CQRS is **not** required for every SDK method; separate public query interfaces from commands when it improves audit/idempotency (e.g., theme publish, checkout session). Avoid Java class/package imitation in TS.

## Inter-repository contracts
Typed OpenAPI from Java for public catalog/cart/checkout facade; versioned management API contract for creator publishing; shared theme manifest JSON schema with hosting service. Contract schemas are released artifacts; changes must pass consumer-driven compatibility suites. No imports from sibling repositories' `src/` and no Git submodule required.

## Security posture
Third-party React browser code is untrusted. SDK allowlisting is convenience, not access control: gateway/Java must enforce tenancy, scopes, rate limits, inventory and pricing. No trusted checkout card collection or admin cookie in theme origin. Theme builds isolated by Storefront backend, not run with CLI user's machine secrets in hosted CI. Review build dependency and SSR capabilities.
