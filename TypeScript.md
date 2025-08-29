# Frontend Development Guide (JavaScript / TypeScript Foundation)

## 1. Introduction

**Purpose:**

Define standards and practices for writing reliable, maintainable, and accessible client-side code, with a strong preference for TypeScript.

**Scope:**

Covers modules, coding standards, async patterns, DOM interaction, accessibility, performance, testing, and security for browser-based applications.

**Audience:**

Frontend developers, reviewers, QA, and tech leads.

**References:**

- ECMAScript Language Spec (ES202x)  
- TypeScript Handbook  
- MDN Web Docs (APIs, DOM, Web Platform)  
- WAI-ARIA Authoring Practices  

---

## 2. Core Principle

**Rule 0: Prefer TypeScript.**

- Write new code in **TypeScript** (`.ts` / `.tsx`).  
- Enable **strict mode** (`"strict": true` in `tsconfig.json`).  
- If plain JS is unavoidable, add **JSDoc types** and enable `@ts-check`.  

---

## 3. Project Structure

- `src/` → application code by domain/feature  
- `src/utils/` → shared utilities (pure functions, no DOM side effects)  
- `src/components/` → UI components (framework-agnostic or specific)  
- `tests/` → unit and integration tests mirroring `src`  

Use **ES modules**. Prefer **named exports** for utilities and components.

---

## 4. Coding Standards

- Use `const` by default; `let` when reassignment is required; never `var`.  
- Use strict equality (`===` / `!==`).  
- Prefer immutability (copy, don’t mutate).  
- camelCase for variables and functions.  
- PascalCase for classes and types.  
- No `any` in production; use `unknown` with narrowing.  
- Model API payloads with **types and runtime validation**.  
- Prefer a validation library like Zod, Valibot or ArkType over standard validation.
- ESLint + Prettier or Biome enforced in CI.  
- Prefer types over interfaces.  Use interface when client facing or creating a library.
- Extend from base line elements when possible.

---

## 5. Modules, Dependencies, and Boundaries

- Keep **pure utilities** (`utils/`) separate from **side-effectful code** (DOM, network).  
- Wrap third-party libraries in **adapters** to isolate change.  
- Prefer **native Web APIs** before adding dependencies.  

---

## 6. Async, Data Fetching, and Cancellation

- Use `async/await` with `try/catch`.  
- Support **cancellation** with `AbortController`.  
- Use **timeouts** to prevent indefinite requests.  
- Distinguish timeout, abort, and HTTP errors.  
- Honor caller-provided signals; make timeout configurable.  

---

## 7. DOM, Events, and Accessibility

- Use **event delegation** for dynamic content.  
- Never use `innerHTML` with untrusted content.  
- Manage **focus** explicitly on dialogs, modals, and navigation changes.  
- Keep ARIA attributes in sync with state (`aria-expanded`, `aria-hidden`, etc.).  

---

## 8. Errors, Logging, and User Feedback

- Fail fast in development; fail gracefully in production.  
- Centralize error handling and logging.  
- Provide user-friendly messages; never expose internals.  
- Do not swallow errors silently.  

---

## 9. Performance

- Batch DOM reads/writes to avoid layout thrashing.  
- Debounce/throttle event handlers.  
- Use lazy loading and code splitting.  
- Memoize hot-path computations when justified.  

---

## 10. Security

- Treat all external input as untrusted.  
- Escape or sanitize before inserting into DOM.  
- Never use `eval`, `Function`, or dynamic code execution.  
- Enforce CSP; avoid inline scripts.  
- Do not leak sensitive information in logs or errors.  

---

## 11. Testing

- Write **unit tests** for utilities.  
- Write **integration tests** for DOM interactions.  
- Use **E2E tests** for flows (e.g., Playwright).  
- Assert accessibility (focus, ARIA states) in tests.  

---

## 12. Tooling & Configuration

- `tsconfig.json`: strict mode and safe compiler options.  
- ESLint: enforce TypeScript rules, no-floating-promises.  
- Prettier: consistent formatting.
- Alternatively, use Biome for linting and formatting.
- CI: run typecheck, lint, test, and bundle-size checks.  

---

## 13. Governance & Review

- PRs must pass typecheck, lint, and tests.  
- Reviewers enforce TypeScript-first and proper module boundaries.  
- Major design changes (API contracts, state management) require architecture review.  
