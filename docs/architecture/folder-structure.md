# Proposed repository and generated theme structures

**Status:** Design blueprint, not files yet. Independent repo, no Turborepo/Nx.

## SDK repository

```text
atlashub_theme_sdk/
├── docs/
│   ├── architecture/
│   ├── guidelines/
│   └── modules/
├── src/
│   ├── domain/
│   │   ├── theme/                 # ThemeId, ThemeVersion, manifest invariants
│   │   ├── settings/              # schemas, defaults, validation and compatibility
│   │   ├── routes/                # required public pages and routing contract
│   │   └── capabilities/          # public API capabilities and scope
│   ├── application/
│   │   ├── ports/                 # HTTP/registry/fs/archive/clock/auth abstractions
│   │   ├── use-cases/             # Validate, Scaffold, Build, Preview, Publish
│   │   └── dto/                   # command/query inputs/results, typed errors
│   ├── infrastructure/
│   │   ├── api/                   # Java public API + Storefront backend clients
│   │   ├── filesystem/            # local project read/write
│   │   ├── build/                 # Vite + React Router build adapter
│   │   ├── archive/               # validated source packaging
│   │   ├── credentials/           # secure OS-backed CLI credential storage
│   │   └── diagnostics/           # log and error mapping
│   ├── presentation/
│   │   ├── cli/                   # commands; thin command handlers
│   │   ├── react/                 # public hooks, provider and error states
│   │   └── scaffold/              # template generation entrypoints
│   └── composition/               # runtime wiring per CLI/React surface
├── templates/
│   └── react-router-vite/         # files copied by create-atlashub-theme
├── examples/
│   └── sample-theme/
├── contracts/
│   ├── theme-manifest.schema.json
│   └── compatibility/            # compatibility fixtures for backend
├── tests/
│   ├── unit/
│   ├── contract/
│   ├── integration/
│   └── e2e/
├── package.json
├── tsconfig.json
└── README.md
```

This is **one related developer-tooling repo**, not a workspace of multiple independent product applications. If publishing distinct npm packages for CLI/SDK/create-theme proves better, arrange package build outputs with standard package tooling; no monorepo orchestrator needed. Do not ship Node-only imports in browser entrypoints. Explicit package `exports` and separate browser/Node bundles are required.

## Generated creator project

```text
fashion-studio/
├── app/
│   ├── routes/                     # home, product, collection, cart, content, 404
│   ├── components/
│   ├── layouts/
│   ├── styles/
│   └── entry.client.tsx             # as required by chosen Router convention
├── public/
│   └── theme-defaults/              # logo, hero, favicon defaults
├── theme.settings.ts                # typed merchant-configurable defaults
├── atlashub.theme.json              # manifest and supported capabilities
├── vite.config.ts
├── react-router.config.ts
├── package.json
├── package-lock.json
└── tsconfig.json
```

The creator can design freely inside approved public routes/components. **No** API keys, `.env` secrets, business database, server admin routes or arbitrary provider webhooks. Generated app uses platform-supplied store context, public API client, safe mocks, SSR hydration and schema validated settings.

## Folder conventions
- `domain` describes meaning/policy; `application` coordinates; `infrastructure` performs I/O; `presentation` adapts callers.
- Path aliases must respect direction, not allow `domain` to import `infrastructure`.
- Co-locate tests with relevant module or central tests by type consistently; avoid giant `utils` catchalls and circular dependencies.
- A reusable public component must remain free of Node build dependencies and privileged portal data.
