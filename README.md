# Atlas Hub Theme Developer SDK, CLI and React Scaffold

**Status: architecture/documentation-only repository; no SDK, CLI, React generator or npm packages have been implemented.**

This independent repository owns the **public developer contract** for third-party Atlas Hub React storefront themes: Vite + React + TypeScript + React Router Framework Mode scaffolding, public commerce SDK, typed themes/settings/page contracts, local preview fixtures, build validation and versioned CLI publishing. Creators can design custom React layouts, components, styles and animations while respecting security boundaries and approved public storefront capabilities.

## Repositories and ownership

| Repository | Responsibility |
|---|---|
| **atlashub_theme_sdk** (this repository) | Public theme SDK, React scaffold, CLI, contracts and developer examples |
| [atlashub_storefront](https://github.com/OlamidotunIY/atlashub_storefront) | Management backend, theme registry, submission/review/build and hosting infrastructure |
| [atlashub_storefront_portal](https://github.com/OlamidotunIY/atlashub_storefront_portal) | Dedicated merchant/creator dashboard |
| [atlashub_auth_web](https://github.com/OlamidotunIY/atlashub_auth_web) | Shared user login, signup and recovery UI |
| [atlashub_backend](https://github.com/OlamidotunIY/atlashub_backend) | Java central identity, independently created organizations, IAM, commerce/stock/orders/checkout, platform billing and merchant-connected provider routing |

There is **no Turborepo, Nx or multi-project monorepo requirement**. This is a standalone developer tooling repo; its SDK, CLI and scaffold are related outputs. Use ordinary package management, TypeScript project references only if justified, and published semantic-versioned npm packages. Developers are not required to clone Atlas Hub backend.

## Architecture standards

Use DDD vocabulary, Clean Architecture dependency direction and Hexagonal Architecture ports/adapters. Apply CQRS to meaningful submission/build/preview commands and catalog queries only; do not build layers around every React hook or simple DTO.

- [DDD/Clean/Hexagonal boundaries](docs/architecture/clean-hexagonal.md)
- [Concrete folder layout and module ownership](docs/architecture/folder-structure.md)
- [Engineering rules, versioning and security](docs/guidelines/engineering-rules.md)
- [Testing, release and CI policy](docs/guidelines/testing-release.md)
- [Module-by-module implementation plan](docs/modules/index.md)

## Developer-facing workflow (planned, **not** published yet)

```bash
npx create-atlashub-theme fashion-studio
cd fashion-studio
npm install
npm run dev
npx atlashub login
npx atlashub theme validate
npx atlashub theme build
npx atlashub theme preview
npx atlashub theme publish --version 1.0.0
```

A theme must include a versioned manifest, required public pages, schema-defined merchant-editable settings **with valid defaults**, public-only SDK, accessibility/SEO contracts, locked dependencies and a Worker-compatible server-rendered build. Theme source goes to a private quarantine/build/review service; no CLI command unilaterally publishes unreviewed code.

Java business data, pricing, checkout, merchant settlement, and provider verification remain server authoritative. Merchant-connected Paystack/Stripe/carrier availability is capability-specific; a business can create its organization with **no connected provider or Atlas-owned banking/KYB**. Theme code never gets business admin APIs, raw payment secrets or direct database access.

**No files in this repo constitute a working theme builder yet.**
