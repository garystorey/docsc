# Frontend Development Guide (React Foundation)

## 1. Introduction

**Purpose:**

Define standards and practices for building reliable, maintainable, and accessible React applications with TypeScript.
See also: ReactComponents.md, ReactHooks.md, TypeScript.md

**Scope:**

Covers component architecture, state and data flow, routing, accessibility, styling integration, performance, testing, and security.

**Audience:**

Frontend developers, reviewers, QA, and tech leads.

**References:**

- React (current stable)
- TypeScript (strict mode)
- MDN Web Docs (Web Platform, ARIA)
- WCAG 2.1

---

## 2. Core Principles

- **Prefer TypeScript** (`.tsx`) with `"strict": true`.
- **One-way data flow**: props down, events up.
- **Functional components** + React Hooks; no class components.
- **Minimal shared state**: lift only when necessary; colocate otherwise.
- **Accessibility by default**: semantic elements, ARIA only to fill gaps.
- **Performance awareness**: render only what’s needed; measure before optimizing.

---

## 3. Project Structure

- `src/` → feature-oriented folders (domain first)
- `src/components/` → reusable UI components (stateless where possible)
- `src/pages/` or `src/routes/` → route-level views
- `src/hooks/` → reusable hooks (pure, framework-safe)
- `src/utils/` → pure utilities (no DOM side effects)
- `src/types/` → shared TypeScript types
- `src/styles/` → global CSS tokens/themes
- `tests/` → mirrors `src/`

**Naming:**

- Components/Hooks: PascalCase (`UserCard`), `useCamelCase` (`useUser`)
- Props/variables: camelCase
- One component per file; named exports

---

## 4. Component Standards

- Keep components **small and focused**; a component = one responsibility.
- **Props are typed** (explicit interfaces). Avoid `any`; use exact, readonly shapes.
- **Children** typing uses `PropsWithChildren` only when children are required.
- **No side effects during render**. Use effects carefully and with stable deps.
- Avoid **implicit state** in closures; rely on state setters and refs as needed.
- **Keys** are stable, deterministic, and not array indices (unless static).

---

## 5. State & Data Flow

- Prefer **local state** for UI concerns; avoid global state by default.
- **Context** sparingly for cross-cutting concerns (theme, auth session, i18n).
- For server data, prefer **fetch-on-render with suspense-ready patterns** or a thin data layer (e.g., tanstack-query/ react-query) that supports caching, revalidation, and **AbortController**.
- Derive state (don’t duplicate); compute from source when feasible.

---

## 6. Async & Data Fetching

- Use `async/await` in event handlers and custom hooks; **never** in render.
- **Cancellation** with `AbortController`; abort on unmount or param change.
- **Timeouts** are configurable; differentiate timeout, abort, and HTTP errors.
- Centralize fetch utilities in `src/utils/` (typed responses + runtime guards).
- Avoid waterfalls: parallelize independent requests; stream or paginate lists.

---

## 7. Routing & Navigation

- Route components are **thin**: compose feature components and hooks.
- Use **loader-like patterns** or dedicated hooks to fetch route data.
- Scroll/focus management: restore focus on route changes for accessibility.
- Handle **not found / unauthorized** at the routing boundary.

---

## 8. Accessibility (A11y)

- Prefer **native elements** (`button`, `a`, `label`, `input`) over divs.
- ARIA only when necessary; keep attributes **in sync with state**.
- Maintain **focus order** and **visible focus indicators**.
- Form controls have explicit labels; error text is programmatically associated. description text should be associated.
- Dynamic content announces changes (live regions) when appropriate.

---

## 9. Styling Integration

- Follow the **CSS Foundation** rules:
  - All colors via **tokens/custom properties**; no hard-coded colors in components.
  - Use `background-color` (not shorthand) unless combining properties.
  - Responsive units by default; `px` only if `< 8px`.
- Components accept **className** to allow composition/overrides.
- Avoid leaking styling details via props; prefer semantic variants (e.g., `variant="primary"`).

---

## 10. Performance

- Prevent **unnecessary re-renders**:
  - Stabilize callbacks/values passed to children (`useCallback`, `useMemo` when it measurably helps).
  - Memoize pure presentational components (`React.memo`) when hot.
- **List virtualization** for large lists; paginate where feasible.
- Defer non-critical work with `requestIdleCallback` or transitions (when applicable).
- Measure with React DevTools Profiler before optimizing.

---

## 11. Error Handling & Boundaries

- Use **error boundaries** around routes and complex trees.
- Distinguish UX errors (validation) from system errors (network).
- Provide **retry** affordances for recoverable failures.
- Log errors with context (feature, action, params), redacting sensitive data.

---

## 12. Forms

- Keep inputs **controlled** where validation and sync matter; uncontrolled is fine for simple cases.
- Validation: prefer **schema-based** (sync/async) at submit and on blur as needed (zod, valibot, ArkType, etc).
- Show inline, accessible errors; prevent submission on invalid state.
- Avoid excessive re-renders by isolating field state where appropriate.

---

## 13. Testing

- **React Testing Library** for component tests; test behavior, not internals.
- Mock network boundaries; don’t mock React APIs.
- Include accessibility assertions (roles, names, focus).
- E2E (e.g., Playwright) for critical flows and cross-page behaviors.

---

## 14. Security

- Never render unsanitized HTML; avoid `dangerouslySetInnerHTML`.
- Escape/encode untrusted data before interpolation.
- Avoid storing secrets in front-end code or environment variables shipped to clients.
- Respect CSP; avoid inline scripts and event handlers.

---

## 15. Tooling & Configuration

- TypeScript: strict mode, noImplicitOverride, exactOptionalPropertyTypes, noUncheckedIndexedAccess.
- ESLint: React + TypeScript rules; disallow `any`, floating promises, implicit `any`, unused vars.
- Prettier: consistent formatting.
- CI: typecheck, lint, test, and bundle size thresholds on PRs.

---

## 16. Governance & Review

- PRs must pass typecheck, lint, tests.
- Reviewers enforce **TypeScript-first**, accessibility, and performance guidelines.
- Introduce new dependencies only with justification (size, maintenance, need).
- Architectural changes (state model, data layer, routing) require design review.
