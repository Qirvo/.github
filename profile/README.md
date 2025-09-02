# Qirvo AI Platform — Monorepo Overview

Qirvo is a personal life management platform that unifies planning, habits, health, tasks, and AI-driven assistance. It’s built as a TypeScript monorepo with a plugin-first architecture so you can extend features via widgets, actions, and services.

---

## What’s in this repo

Top-level workspaces (folders are siblings of this .github directory):

- qirvo — Main Next.js 15 dashboard (App Router) with plugin marketplace and widget system.
- qirvo-plugin-sdk — TypeScript SDK for building Qirvo plugins.
- qirvo-echo-cli — Node.js CLI that authenticates with Qirvo and syncs actions/automation.
- qirvo-admin-dashboard — Turborepo-based admin interface.
- qirvo-website — Public marketing site (Next.js + Tailwind).
- qirvo-weather-plugin — Example weather plugin demonstrating manifests and widgets.
- qirvo-agent-plugin — Example AI agent plugin with a dashboard widget.

> Tip: Use your IDE’s “Go to file” to jump into any of these folders quickly.

---

## Tech stack

- Framework: Next.js 15 (App Router) + TypeScript
- UI: Mantine UI + Zustand for client state
- Auth: Firebase Auth (client) + Firebase Admin (server)
- Database: MongoDB Atlas
- Plugins: Manifest-driven, secure runtime with CommonJS `require()` support (JS/TS/TSX)
- Testing: Jest with a 90% coverage target

---

## Architecture at a glance

### Plugin system

- Canonical manifest schema (with adapters for legacy manifests).
- Widgets: visual units rendered in dashboard areas (landing, daily, sidebar, etc.).
- Runtime: sandboxed execution with module resolution for JS/TS/TSX components.
- Validation & Security: manifests validated, Content Security Policy enforced, and permissions honored.
- Storage precedence for plugin files: user-installed → private storage → public registry.

### Authentication & API security

- Client obtains Firebase ID token; server verifies it via Firebase Admin on every API call.
- Standard API route structure: extract Bearer token → verify → connect to MongoDB → perform sanitized queries.
- No client-side trust for authorization; server performs all checks.

---

## Key packages

### qirvo (Dashboard)

- Next.js app hosting the dashboard, marketplace, and widgets UI.
- Server Actions used where appropriate; Zustand for client state.
- WidgetArea dynamically loads widgets a user has added and renders plugin components through the runtime.

### qirvo-plugin-sdk (SDK)

- Types/utilities for plugin authors.
- Manifest schema, config validation, and packaging guidance.

### qirvo-echo-cli (CLI)

- Auth via the dashboard, then local automation and sync from your terminal.

### qirvo-weather-plugin (Example)

- A reference plugin showcasing `dashboard_widget`, config schema, and packaging.

### qirvo-agent-plugin (AI)

- An AI-focused plugin exposing a configurable widget.

---

## Local development

Run the main app (dashboard):

```powershell
cd qirvo
npm run dev
```

Health checks (typecheck, lint, tests):

```powershell
cd qirvo
npm run health
```

Builds:

```powershell
# Production build
cd qirvo
npm run build && npm start

# Static build (Firebase)
npm run build:static
```

> Some features (Server Actions, API routes) require a full Node environment; pure static hosting disables these.

---

## Testing & CI

- Jest tests under `__tests__/` with ≥90% coverage.
- Typical CI (GitHub Actions) runs lint, type-check, coverage, and builds across workspaces.

```yaml
name: CI
on:
  pull_request:
  push:
    branches: [ main ]
jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20' }
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check
      - run: npm run test:ci --workspaces -- --coverage
      - run: npm run build --workspaces
```

---

## Security & privacy

- All API routes verify Firebase ID tokens server-side.
- Sensitive config is not logged; PII is avoided in runtime logs.
- Plugins are validated and executed under enforced CSP and explicit permissions.

---

## Building plugins

1. Use `qirvo-plugin-sdk` for manifest types and helpers.
2. Implement components for your `dashboard_widget` (React, TS/TSX supported).
3. Validate and test with the dashboard dev server (hot reload supported in dev).
4. Package and publish (or install privately to your account).

Common manifest fields:

- `dashboard_widget` (name, component, description, size/defaultSize, configSchema)
- `config_schema` (fields, types, validation rules)
- `permissions` (e.g., storage, network)

---

## Contributing

- Use feature branches: `feat/...`, `fix/...` (Conventional Commits preferred).
- Keep PRs small and focused; update tests and docs with behavior changes.
- Respect workspace boundaries and security patterns (Firebase Admin, sanitized DB access).

---

## Support & community

- Open issues in the relevant workspace folder.
- For security disclosures, use private channels—do not include sensitive details in public issues.

---

## License

Refer to the individual package folders for license details.

---

## Quick facts

- Next.js + TypeScript + Mantine + Zustand
- Firebase Auth (client) + Firebase Admin (server)
- MongoDB Atlas for data
- Manifest-driven plugin system with a secure runtime
- SDK, CLI, example plugins, admin tools, and a marketing site in one monorepo
