# Frontend Development Guide (Angular 19+ Foundation)

## 1. Introduction

**Purpose:**

Define standards and practices for building reliable, maintainable, accessible Angular apps with TypeScript.

**Scope:**

Covers component architecture, templates/control flow, state/data, routing, forms, SSR/hydration, performance, testing, security, and tooling.

**Audience:**

Frontend developers, reviewers, QA, and tech leads.

**References:**

- [Angular Docs](https://angular.dev/)
- [Legacy Angular.io](https://angular.io/)
- [TypeScript](https://www.typescriptlang.org/)
- [RxJS](https://rxjs.dev/)
- [WCAG 2.1](https://www.w3.org/TR/WCAG21/)

---

## 2. Core Principles

- **Prefer TypeScript** with `"strict": true`.
- **Standalone-first:** use standalone components, directives, and pipes (no NgModules).
- **Signals-first:** favor Angular **signals** for local UI state; compose with computed/effects.
- **Modern templates:** use `@if`, `@for`, `@switch` control flow (instead of `*ngIf/*ngFor/*ngSwitch`).
- **Zoneless-ready:** always prefer **zoneless** change detection with signals and explicit notifications.
- **Change detection:** Prefer `ChangeDetectionStrategy.OnPush` by default for app components. It reduces unnecessary work and aligns with signals and zoneless apps. Note: there is no global runtime switch; configure schematics so new components default to OnPush.

- **SSR + Hydration:** enable SSR and client hydration for performance/SEO-sensitive routes.

**Recommended tools:**

- [angular-eslint](https://github.com/angular-eslint/angular-eslint)
- [Prettier](https://prettier.io/)
- [Angular DevTools](https://angular.dev/tools/devtools)

---

## 3. Project Structure

- `src/` → feature/domain-oriented folders  
- `src/components/` → reusable presentational components (each component has `.component.ts`, `.component.html`, `.component.css|.scss`)  
- `src/routes/` → route-level views  
- `src/services/` → shared injectables (API clients, global services)  
- `src/state/` → reusable signal-based state containers (optional, for app/feature-level state)  
- `src/utils/` → pure utilities (no DOM side effects)  
- `src/styles/` → global tokens/themes (CSS custom properties)  
- `src/types/` → shared TypeScript types  
- `tests/` → mirrors `src/`

**Naming:**

- Files: **kebab-case** (e.g., `user-card.component.ts`)
- Components/Directives/Pipes: **PascalCase**
- Props/vars: **camelCase**
- One component per file; **named exports**

---

## 4. Component Standards

- **Standalone** components with explicit `imports` (no NgModules).  
- Strictly typed **inputs/outputs**; avoid `any`.  
- Keep components **small and focused**; one responsibility.  
- No side effects during render; use effects and lifecycle hooks deliberately.  
- Keys for lists are stable/deterministic.  
- **Prefer external templates and styles:** use `templateUrl` and `styleUrls` instead of inline `template`/`styles`.  
- **Naming (Angular standard):** `kebab-case` filenames — `feature-name.component.ts`, `feature-name.component.html`, `feature-name.component.css|.scss`.  
- Keep templates presentational; move logic to the component class (signals, computed).
- Use `ChangeDetectionStrategy.OnPush` unless you have a clear reason not to (e.g., library components hosting unknown children).
- HTTP best practice: Use HttpClient with toSignal and takeUntilDestroyed() by default. Reserve native fetch for very simple one-off calls (no interceptors). Reach for RxJS operators only when streaming/composition truly adds value.
- Set OnPush as the **default in schematics** so all generated components start with it:

  ```json
  {
    "schematics": {
      "@schematics/angular:component": {
        "changeDetection": "OnPush"
      }
    }
  }
  ```

**Resources:**

- [Standalone APIs](https://angular.dev/guide/components#standalone-components)
- [Signals](https://angular.dev/guide/signals)

---

## 5. Templates & Control Flow

- Prefer **`@if/@for/@switch`** for readability and performance.
- Keep templates simple; compute in the component via **signals** and `computed`.
- Use **`@defer`** for heavy, non-critical UI.
- **`linkedSignal` (writable derived state):** Use when a value should be *derived from other signals by default* but still be *manually overridable*. A `linkedSignal` recomputes when its source changes and remains writable for user-driven updates.

  ```ts
  import { signal, linkedSignal } from '@angular/core';

  readonly options = signal(['Ground', 'Air', 'Sea']);
  selected = linkedSignal(() => options()[0]); // default tracks options

  // user override remains until options() changes again
  select = (i: number) => selected.set(options()[i]);
  ```

**Resources:**

- [Control Flow](https://angular.dev/guide/templates/control-flow)
- [Deferrable Views (`@defer`)](https://angular.dev/guide/templates/defer)
- [`linkedSignal` Guide](https://angular.dev/guide/signals/linked-signal)

---

## 6. State & Data

- **Local/UI state:** Angular **signals** (`signal`, `computed`, `linkedSignal`) for fine-grained reactivity.
- **Global/app state:** use **signal-based injectable services** that expose signals and update methods.
- **Streams & async:** use **RxJS** for event streams and async data; keep UI state in **signals**.
- **Persistence & sync:** selectively persist (e.g., to `localStorage`); sync cross-tab with the `storage` event.
- Avoid heavy state frameworks unless absolutely necessary.

```ts
// src/state/useCounter.ts
import { signal } from '@angular/core';

export function useCounter() {
  const count = signal(0);

  const inc = () => count.update(v => v + 1);
  const dec = () => count.update(v => v - 1);
  const reset = () => count.set(0);

  return { count, inc, dec, reset };
}
```

**Resources:**

- [Signals Overview](https://angular.dev/guide/signals)
- [RxJS](https://rxjs.dev/)

---

## 7. Routing & Navigation

- Use **`provideRouter()`** with standalone routes; enable features as needed.
- Route-based code splitting and preloading where beneficial.
- Manage scroll and focus on navigation for accessibility.

**Resources:**

- [`provideRouter` API](https://angular.dev/api/router/provideRouter)

---

## 8. HTTP, Async & Cancellation

- Provide HttpClient via **`provideHttpClient()`**; prefer **`withFetch()`** (works with SSR).
- Use **interceptors** for auth, logging, error mapping.
- Distinguish timeout vs. abort vs. HTTP errors; retries explicit only for idempotent requests.

**Resources:**

- [`provideHttpClient` API](https://angular.dev/api/common/http/provideHttpClient)

---

## 9. Forms

- Prefer **Reactive Forms** with strict typing and schema-backed validation.
- Keep validation messages accessible and in sync with control state.

**Resources:**

- [Reactive Forms (angular.dev)](https://angular.dev/guide/forms/reactive-forms)
- [Forms API (angular.io)](https://angular.io/api/forms)

---

## 10. Styling Integration

- Follow the [CSS Foundation](./CSS.md):
  - All colors via **CSS custom properties**.
  - Use `background-color` (not shorthand) unless combining properties.
  - **Responsive units** by default; `px` only if **< 8px**.
- Components accept `class` for composition; avoid style leakage.

---

## 11. Performance

- Use **`@defer`** for non-critical UI.
- Prefer **zoneless** + signals where viable to reduce checks.
- Virtualize large lists; paginate; memoize hot paths after measuring.

**Resources:**

- [Zoneless Guide](https://angular.dev/guide/zoneless)
- [Deferrable Views](https://angular.dev/guide/templates/defer)
- [Angular DevTools](https://angular.dev/tools/devtools)

---

## 12. SSR, SSG & Hydration

- Add SSR and enable **hydration** for faster first paint and SEO as needed.
- Author server-compatible code (no direct DOM globals during SSR).
- Consider SSG for mostly static sections.

**Resources:**

- [SSR / Hybrid Rendering](https://angular.dev/guide/ssr)
- [Hydration](https://angular.dev/guide/hydration)

---

## 13. Testing

- Always prefer **Vitest** for unit/integration tests (faster, modern tooling).
- **Component tests:** use **Angular Testing Library** to test via roles/names (a11y-centric).
- **E2E tests:** use **Playwright**.

**Recommended tools:**

- [Vitest](https://vitest.dev/)
- [Angular Testing Library](https://testing-library.com/docs/angular-testing-library/intro/)
- [Playwright](https://playwright.dev/)

---

## 14. Tooling & Build

- **Angular CLI** with the modern **esbuild/Vite** builder for dev & prod.
- **angular-eslint** for linting; enforce in CI.
- **Angular DevTools** for debugging and profiling.

**Resources:**

- [Build System Migration](https://angular.dev/tools/cli/build-system-migration)
- [angular-eslint](https://github.com/angular-eslint/angular-eslint)
- [Angular DevTools](https://angular.dev/tools/devtools)

---

## 15. Accessibility (A11y)

- refer to [HTML](./HTML.md)
- Prefer semantic elements; ARIA only to fill gaps.
- Maintain visible focus and logical tab order.
- Associate labels/help/error text with controls; announce dynamic updates appropriately.
- Follow the rules of ARIA.

---

## 16. Security

- Treat all external input as untrusted; avoid `innerHTML` with untrusted data.
- Follow Angular sanitization rules; bypass APIs only when strictly necessary.
- Enforce **CSP** and **Trusted Types** on production origins.

**Resources:**

- [Angular Security Guide](https://angular.io/guide/security)

---

## 17. Governance & Review

- PRs must pass **typecheck, lint, tests**, and bundle/report checks.
- Review for **signals-first**, **standalone-first**, a11y, and performance hotspots.
- Justify new dependencies (size, need, maintenance).

## 18. Example Component

### Component.ts

```ts
// src/components/product-list/product-list.component.ts
import {
  Component,
  ChangeDetectionStrategy,
  inject,
  signal,
  linkedSignal,
  computed,
} from '@angular/core';
import { CommonModule } from '@angular/common';
import { HttpClient } from '@angular/common/http';
import { toSignal, takeUntilDestroyed } from '@angular/core/rxjs-interop';
import { catchError, startWith } from 'rxjs/operators';
import { of } from 'rxjs';

/** Domain types */
type Product = { id: string; name: string; priceCents: number };

/** Data client (in real apps: move to src/services/) */
class ProductApi {
  private http = inject(HttpClient);

  /** Returns Observable<Product[]> */
  list() {
    return this.http.get<Product[]>('/api/v1/products');
  }
}

@Component({
  selector: 'app-product-list',
  standalone: true,
  imports: [CommonModule],
  changeDetection: ChangeDetectionStrategy.OnPush, // default via schematics recommended
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css'],
})
export class ProductListComponent {
  /** State */
  private api = new ProductApi();

  readonly error = signal(false);
  readonly query = signal('');

  /** Stream -> Signal (base Angular) */
  private readonly products$ = this.api
    .list()
    .pipe(
      startWith(null as Product[] | null), // first emission signals "loading"
      catchError(() => {
        this.error.set(true);
        return of([] as Product[]); // recover with empty list
      }),
      takeUntilDestroyed()
    );

  /** null during loading; array thereafter */
  readonly products = toSignal<Product[] | null>(this.products$, { initialValue: null });

  /** Derived */
  readonly loading = computed(() => this.products() === null);

  readonly filtered = computed(() => {
    const q = this.query().trim().toLowerCase();
    const items = (this.products() ?? []) as Product[];
    return q ? items.filter((p) => p.name.toLowerCase().includes(q)) : items;
  });

  /** Writable derived state: defaults to first filtered item, user can override */
  readonly selected = linkedSignal<Product | null>(() => this.filtered()[0] ?? null);

  /** Handlers */
  onQuery(value: string) {
    this.query.set(value);
  }

  select(p: Product) {
    this.selected.set(p);
  }

  /** Utils (pure) */
  formatPrice(cents: number): string {
    return new Intl.NumberFormat(undefined, { style: 'currency', currency: 'USD' }).format(
      cents / 100
    );
  }
}
```

### Component.html

```html
<!-- src/components/product-list/product-list.component.html -->
<section aria-labelledby="products-heading">
  <div class="toolbar">
    <h2 id="products-heading">Products</h2>

    <div aria-live="polite" class="sr-only">
      {{ filtered().length }} products available
    </div>
  </div>

  <div class="search">
    <label for="q">Search</label>
    <input
      id="q"
      type="search"
      [value]="query()"
      (input)="onQuery(($event.target as HTMLInputElement).value)"
      placeholder="Type to filter…"
      autocomplete="off"
    />
  </div>

  @if (loading()) {
    <p role="status">Loading…</p>
  } @else if (error()) {
    <p role="alert">Sorry, something went wrong. Please retry.</p>
  } @else {
    <ul class="list" role="list">
      @for (p of filtered(); track p.id) {
        <li>
          <button
            type="button"
            class="item"
            [attr.aria-pressed]="selected()?.id === p.id"
            (click)="select(p)"
          >
            <span>{{ p.name }}</span>
            <span class="price">{{ formatPrice(p.priceCents) }}</span>
          </button>
        </li>
      } @empty {
        <p>No products match your search.</p>
      }
    </ul>
  }

  @defer (when selected(); prefetch on idle) {
    @if (selected(); as s) {
      <aside class="details" aria-labelledby="details-heading">
        <h3 id="details-heading">Details</h3>
        <p><strong>Name:</strong> {{ s.name }}</p>
        <p><strong>Price:</strong> {{ formatPrice(s.priceCents) }}</p>
      </aside>
    }
  }
</section>
```

### component.styles.css

```css

/* src/components/product-list/product-list.component.css */
:host {
  display: grid;
  gap: var(--space-3);
}

.toolbar {
  display: grid;
  grid-template-columns: 1fr auto;
  gap: var(--space-2);
  align-items: center;
}

.search {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: var(--space-2);
  align-items: center;
}

.search input {
  font: inherit;
  padding: var(--space-2);
  border-radius: var(--radius-1);
  border: 1px solid rgba(0, 0, 0, 0.12);
  width: 100%;
}

.list {
  display: grid;
  gap: var(--space-2);
}

.item {
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: center;
  padding: var(--space-3);
  border-radius: var(--radius-1);
  background-color: var(--color-surface);
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.06);
}

.item[aria-pressed='true'] {
  outline: 2px solid var(--color-primary); /* < 8px per CSS guide */
  outline-offset: 0.125rem;
}

.price {
  font-weight: 600;
}

.details {
  padding: var(--space-3);
  border-radius: var(--radius-1);
  background-color: var(--color-background);
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

```
