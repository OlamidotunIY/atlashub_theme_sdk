# Theme SDK — Module-by-module architecture and development order

**Status:** Planned, no source implemented. Implementation of each module must follow [clean/hexagonal architecture](../architecture/clean-hexagonal.md), [folder layout](../architecture/folder-structure.md), [rules](../guidelines/engineering-rules.md), and [tests](../guidelines/testing-release.md).

| Bounded context | Core responsibility | Ports/adapters | Acceptance |
|---|---|---|---|
| ThemeDefinition | Theme manifest, IDs, versions, supported pages/capabilities | SchemaValidator, JSON loader | Reject missing/unsupported required pages and inconsistent semver |
| ThemeSettings | Typed fields/defaults, merchant override compatibility/migrations | SettingsSchemaValidator | Every configurable field has valid default or explicit optional/null safe behavior |
| ThemeRoutes | Public route declarations, market/locale page contract | React Router scaffold adapter | Products, collections, home, cart and content render as specified |
| PublicStorefrontApi | Product/collection queries, cart operations, checkout initiation | Java public storefront HTTP + mock fixtures | Tenant context attested server-side, typed errors, no admin endpoints |
| CreatorCredentials | CLI login, scoped token, revoke/logout | IdentityAuthGateway, CredentialStore | Secure login, no tokens in source/redirects, expiry handled |
| ThemeScaffolding | New Vite React Router project | FileAccess, TemplateCopier | One-command generated project compiles and mocks product data |
| ThemeValidation | Settings/routes/manifest/dependency/security checks | Filesystem and schema adapters | Clear actionable errors and deterministic diagnostics |
| ThemeBuild | SSR/client Vite bundling and controlled package deps | BuildExecutor, ArtifactHasher | Compatible Cloudflare user Worker output and hydrated browser bundle |
| CreatorPublishing | Upload source candidate, status, review and unlisting | ThemeRegistryGateway | Submit never bypasses review; immutable version and idempotency |
| ExampleThemes | Document framework for third-party developers | Fixtures and public SDK | Two distinct demo themes, no secret keys |

## Delivery slices

**Foundation:** value objects + manifest/settings schema, typed public API gateway/fakes, sample data; test independent of React.

**Developer MVP:** create-theme scaffold and route contract with settings defaults, React SDK read-only catalog queries and tests.

**Build and preview:** SSR/client bundling and Cloudflare compatibility test, local sandbox preview, size/dependency scans and accessible SEO page metadata.

**Publishing:** central CLI SSO, source archive validation/upload, polling/review statuses, immutability/versioning, integration tests with separate Storefront management API.

**Ecommerce:** trusted cart SDK/checkout initiation (Java validates price/stock/provider capabilities), typed order status handoff, international locale and currency contracts.

**Hardening:** security adversarial tests, backward/forward compatibility and upgrade guide, multi-runtime/Node versions, signed packages and change logs.

## Existing backend transitions
Global user registration is separate from organization creation; creator need not own a business. Atlas Hub backend no longer requires compulsory Anchor-managed banking/KYB. Payment capability uses merchant-selected eligible provider connections; SDK never implements its own processor or bypasses provider onboarding.

## Definition of done for a module
Layer dependency tests, domain/unit and adapter/integration tests, contract fixture, public API docs, README/example updates, threat review for new surface, semver notes and passing CI. No demo/mock presented as production behavior.
