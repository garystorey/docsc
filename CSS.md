# Frontend Development Guide (CSS Foundation)

## 1. Introduction

**Purpose:**

This guide defines standards and practices for writing consistent, scalable, and maintainable CSS.

**Scope:**

Applies to all styling in projects that use plain CSS, regardless of whether a preprocessor or framework is later introduced.

**Audience:**

Frontend developers, UX designers, and QA engineers.

**References:**

- [MDN CSS Reference](https://developer.mozilla.org/en-US/docs/Web/CSS)
- [W3C CSS Standard](https://www.w3.org/Style/CSS/)
- [WCAG 2.1 Accessibility Guidelines](https://www.w3.org/TR/WCAG21/)

---

## 2. System Overview

CSS provides the **presentation layer** for web applications. It controls visual design, layout, and responsiveness while keeping structure (HTML) and behavior (JS) separate.

---

## 3. Design Principles & Goals

- **Separation of concerns:** Keep HTML semantic and CSS focused only on styling.
- **Maintainability:** Ensure CSS is easy to read, debug, and refactor.
- **Consistency:** Use a unified style system (design tokens/variables).
- **Performance:** Minimize unused CSS; avoid overly deep selector nesting.
- **Accessibility:** Maintain contrast ratios, readable text, and visible focus indicators.
- **Responsiveness:** Mobile-first design that scales up gracefully.

---

## 4. Architecture & Patterns

### 4.1 CSS Organization

Use a **modular structure**:

- `base/` → resets, global styles, typography
- `components/` → buttons, forms, cards
- `layouts/` → grids, flex containers, wrappers
- `utils/` → helpers like `.visually-hidden`, `.sr-only`
- when using a framework, refer to the framework document for more precise information.

### 4.3 Layout Patterns

- Use **Flexbox** for one-dimensional layouts (navbars, toolbars, alignment).
- Use **CSS Grid** for two-dimensional layouts (page grids, dashboards).
- Avoid table-based layouts for non-tabular data.

### 4.4 Responsive Design

- **Mobile-first:** Start with base styles; enhance with min-width media queries.
- Prefer **responsive units** (`rem`, `em`, `%`, `vh`, `vw`, `ch`). Use `px` only when the value is **< 8px** (e.g., 1px borders).
- Define standard breakpoints (example—adjust to your design system):

```css
@media (min-width: 36rem) { /* ~576px if root is 16px */ }
@media (min-width: 48rem) { /* ~768px */ }
@media (min-width: 62rem) { /* ~992px */ }
@media (min-width: 75rem) { /* ~1200px */ }
```

### 4.5 Theming & Variables

- Use **CSS Custom Properties** for design tokens:
- Use `background-color` for single-property color application. Use `background` **only** for true shorthand cases (e.g., image + position + size).
- Ensure light-dark() settings are set.
- Ensure system settings for dark light mode are enforced.

```css
:root {
  --color-primary: #1e90ff;
  --color-secondary: #ff9800;
  --color-surface: #ffffff;
  --color-text: #333333;
  --color-background: #f7f7f8;

  --font-family-base: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --font-size-base: 1rem;
  --line-height-base: 1.5;

  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-3: 1rem;     /* 16px */
  --radius-1: 0.25rem; /* 4px */
}
```

---

## 5. Coding Standards & Guidelines

- **Indentation:** 2 spaces.
- **Selectors:** Prefer classes; avoid IDs for styling. Keep selectors short and intention-revealing (`.nav-item`, not `header ul li a`).
- **Specificity:** Keep specificity low; never use `!important` unless specifically directed. Otherwise, use modern CSS techniques to manage specificity.
- **Property order (recommended):** layout → box-model → typography → visual → effects → transitions.
- **Units:**  
  - Use **responsive units by default** (`rem`, `em`, `%`, `vh`, `vw`, `ch`).  
  - Use `px` **only** when the value is **under 8px** (e.g., borders, hairlines, fine shadows).  
  - Use **unitless** `line-height` for readability and scaling.
- **Background properties:**  
  - Use `background-color` when setting only color.  
  - Use `background` shorthand **only** when combining multiple properties.
- **Comments:** Document intent and non-obvious decisions with concise comments.
- Always prefer modern CSS syntax.

Example ordering and units:

```css
.card {
  display: grid;                            /* layout */

  max-width: 40rem;                         /* box-model */
  padding: var(--space-3);
  border-radius: var(--radius-1);

  color: var(--color-text);                 /* typography */
  font-size: var(--font-size-base);
  line-height: var(--line-height-base);

  background-color: var(--color-surface);   /* visual */
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.08);

  transition: box-shadow 150ms ease;        /* effects/transitions */
}
```

---

## 6. UI/UX Guidelines

- **Contrast:** Maintain at least 4.5:1 for body text; 3:1 for large text.
- **Focus:** Always provide visible focus states for interactive elements.
- **Color semantics:** Don’t rely solely on color to convey meaning; use icons/labels.
- **Color tokens:** All colors in component CSS **must come from tokens** (`--color-*`). Do not use raw hex, RGB, or HSL values in component styles.
- **Typography:** Use a consistent scale (`h1` > `h2` > `h3`), unitless line-height `1.4–1.6`.
- **Spacing:** Use tokenized spacing (`--space-*`) in `rem`. Avoid arbitrary one-off values.

---

## 7. Non-Functional Requirements

- **Performance:** Remove unused CSS (e.g., build-time purging). Keep critical CSS lean.
- **Accessibility:** Adhere to WCAG 2.1 AA for contrast and focus as a strict minimum. Strive for AAA where possible.
- **Scalability:** New components must compose existing tokens without redefining globals.

---

## 8. Tooling & Environment

- **Linters:** Stylelint with a shared config (naming conventions, unit rules).
- **Formatter:** Prettier (respecting CSS formatting).
- **Testing:** Cross-browser (Chrome, Firefox, Safari, Edge). Use responsive mode in dev tools.
- **Debugging:** Use dev tools for Grid/Flex overlays and accessibility checks.

---

## 9. Governance & Review

- **Code review:** Validate naming, specificity, token usage, accessibility.
- **Documentation:** Each component should include examples/states (default, hover, active, disabled, focus).
- **Change control:** Token changes (colors, spacing, typography) require design sign-off.

---

## 10. Appendices

### Example Base CSS

```css
/* Base & tokens */
:root {
  --color-primary: #1e90ff;
  --color-secondary: #ff9800;
  --color-surface: #ffffff;
  --color-text: #333333;
  --color-background: #f7f7f8;

  --font-family-base: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --font-size-base: 1rem;
  --line-height-base: 1.5;

  --space-1: 0.25rem;  /* 4px */
  --space-2: 0.5rem;   /* 8px */
  --space-3: 1rem;     /* 16px */
  --radius-1: 0.25rem; /* 4px */
}

*,
*::before,
*::after {
  box-sizing: border-box;
}

html {
  /* Users can change base size; rem scales accordingly */
  font-size: 100%;
}

body {
  margin: 0;
  font-family: var(--font-family-base);
  font-size: var(--font-size-base);
  line-height: var(--line-height-base);
  color: var(--color-text);
  background-color: var(--color-background);
}

/* Layout container */
.container {
  width: min(100%, 72rem);
  margin-inline: auto;
  padding-inline: var(--space-3);
}

/* Button component */
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-1);

  padding: calc(var(--space-1) + 0.125rem) var(--space-3);
  border: 0; /* borders may use px if < 8px */
  border-radius: var(--radius-1);

  font-weight: 600;
  line-height: 1;

  color: var(--color-surface);
  background-color: var(--color-primary);

  transition: filter 150ms ease;
}

.button:hover {
  filter: brightness(1.05);
}

.button:focus-visible {
  outline: 2px solid currentColor; /* 2px allowed (< 8px) */
  outline-offset: 0.125rem;
}

.button--secondary {
  background-color: var(--color-secondary);
}

/* Card component */
.card {
  display: grid;
  gap: var(--space-2);
  padding: var(--space-3);
  border-radius: var(--radius-1);
  background-color: var(--color-surface);
  box-shadow: 0 0.25rem 0.5rem rgba(0, 0, 0, 0.08);
}

/* Media utility */
.media-cover {
  /* Correct shorthand usage: multiple background properties together */
  background: url("banner.jpg") no-repeat center / cover;
}
```
