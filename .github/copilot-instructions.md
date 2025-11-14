# Copilot instructions for ns-ens-playground

This repo is a small playground organized as a mono-repo with a single active app and a mini test app. The goal is fast iteration on React UI with Vite; backend/lib folders are placeholders.

## Big picture
- Structure:
  - `frontend/`: React 19 + TypeScript + Vite 7 app (active app).
  - `mini-test-apps/test-ens-lookup/test-ens-lookup-vite/`: throwaway/prototype React app scaffold (same stack).
  - `backend/`, `lib/`: empty placeholders (`.gitkeep`). No server code yet.
- Entry points:
  - `frontend/src/main.tsx` mounts <App /> into the element with id "root" using `StrictMode`.
  - `frontend/src/App.tsx` is a basic Vite template (no routing/state mgmt beyond local state).
- Build output: Vite static build to `frontend/dist` (default).
- Deployment: GitHub Pages via GitHub Actions. PR previews deploy to `/pr-{number}/` subdirectories; Vite `base` path is configured via `VITE_BASE_PATH` env var in CI.

## Tooling and workflows
- Package manager: Yarn is implied by `frontend/yarn.lock`. If unsure, prefer Yarn in app folders.
- Scripts (run inside each app folder):
  - Dev: `yarn dev` (Vite dev server).
  - Build: `yarn build` (TypeScript build + `vite build`).
  - Preview: `yarn preview` (serves built `dist`).
  - Lint: `yarn lint` (ESLint flat config).
- Node/TS:
  - TS config uses bundler resolution and ESM: `moduleResolution: "bundler"`, `verbatimModuleSyntax: true`, `allowImportingTsExtensions: true`. Preserve import file extensions and ESM syntax in edits.
  - React Compiler is enabled via `babel-plugin-react-compiler` in `vite.config.ts`. Expect slower builds but improved runtime hints.
- CI/CD:
  - PR previews: `.github/workflows/on-pr-merge-request.yaml` builds frontend with `VITE_BASE_PATH=/ns-ens-playground/pr-{number}/` and deploys to GitHub Pages subdirectory.
  - Vite `base` path defaults to `/` locally, overridden by `VITE_BASE_PATH` env var in CI to handle subdirectory deployments.

## Conventions to follow
- Components live under `frontend/src/` as `.tsx`. Global styles in `index.css` and per-component styles like `App.css`.
- Prefer ESM with explicit extensions where present, e.g. `import App from './App.tsx'`. Do not convert to CommonJS.
- Avoid side-effect-only imports unless intended: TS config enables `noUncheckedSideEffectImports`.
- Keep code strict and dead-code free: `strict: true`, `noUnusedLocals/Parameters: true`.
- ESLint flat config (`frontend/eslint.config.js`) includes `react-hooks` and `react-refresh` presets. Fix issues or suppress with minimal, local `eslint-disable-next-line` comments when justified.

## Adding features safely
- New UI feature: create a component under `frontend/src/` and wire it in `App.tsx`. Example import style: `import MyWidget from './MyWidget.tsx'`.
- Assets: import via Vite (e.g., `import logo from './assets/logo.svg'`) or place in `frontend/public/` for static paths.
- Prototypes: place experimental code in `mini-test-apps/test-ens-lookup/test-ens-lookup-vite` to avoid polluting `frontend/`.

## Integration points
- No external APIs or ENS libraries are wired yet. When adding web3/ENS code, constrain it to the app folder and keep build-time deps compatible with Vite ESM (e.g., prefer ESM-ready libs).

## Files to know
- `frontend/vite.config.ts` – React plugin + React Compiler.
- `frontend/tsconfig.*.json` – bundler/ESM-focused TS settings.
- `frontend/eslint.config.js` – ESLint flat config.
- `frontend/src/main.tsx`, `frontend/src/App.tsx` – app entry and shell.

If anything here is unclear or you need additional conventions documented (tests, routing, ENS integrations), call it out and we’ll iterate.