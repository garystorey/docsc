# Frontend Development Guide (HTML Foundation)

## 1. Introduction

**Purpose:**  
This guide defines standards and practices for writing consistent, accessible, and maintainable HTML code.  

**Scope:**  
Applies to all web pages and UI components built with HTML before layering CSS, JavaScript, or frameworks.  

**Audience:**  
Frontend developers, UX designers, QA engineers, and technical reviewers.  

**References:**

- [MDN HTML Reference](https://developer.mozilla.org/en-US/docs/Web/HTML)  
- [W3C HTML Standard](https://html.spec.whatwg.org/)  
- [WCAG 2.1 Accessibility Guidelines](https://www.w3.org/TR/WCAG21/)  

---

## 2. System Overview  

The HTML layer forms the **structural foundation** of the frontend. It provides semantic meaning to content, enabling:  

- Accessibility (screen readers, assistive tech).  
- SEO optimization.  
- Consistency across browsers and devices.  

---

## 3. Design Principles & Goals  

- **Semantic HTML first:** Always use the most appropriate HTML element for content.  
- **Separation of concerns:** Keep structure (HTML), presentation (CSS), and behavior (JS) distinct.  
- **Accessibility:** Follow ARIA and WCAG standards. Follow the Rules of Aria.
- **Performance:** Use minimal markup, avoid unnecessary wrappers.  
- **Maintainability:** Code should be readable, consistent, and validated.  

---

## 4. Architecture & Patterns

### 4.1 Document Structure  

- Always start with a `<!DOCTYPE html>` declaration.  
- Use `<html lang="en">` to specify language.  
- Define metadata in `<head>` (title, description, charset, viewport).  
- Organize `<body>` content with semantic elements:  
  - `<header>` – site or section header  
  - `<nav>` – primary navigation  
  - `<main>` – unique page content  
  - `<section>` – grouped related content  
  - `<article>` – standalone content item  
  - `<aside>` – supplementary content  
  - `<footer>` – site or section footer  

### 4.2 Accessibility Patterns  

- Every page must have **one `<h1>`**.  
- Use headings (`<h2>…<h6>`) in **logical nesting order**.  
- Provide **alt text** for images.  
- Use **labels** with form inputs.  
- Use **landmark roles** (`role="navigation"`, `role="main"`) sparingly (only when native HTML isn’t sufficient).  

### 4.3 Common Components  

- **Navigation:** Use `<nav>` with an unordered list `<ul><li><a></a></li></ul>`.  
- **Forms:** Use `<form>`, `<label for="">`, and appropriate input types (`email`, `tel`, `date`).  Make sure descriptions are linked via aria-describedby.
- **Tables:** Use `<thead>`, `<tbody>`, `<tfoot>`, and `<th scope="col|row">`.  

---

## 5. Coding Standards & Guidelines  

- **Indentation:** 2 spaces.  
- **Attributes:** Lowercase, quoted values (`<input type="text" name="username">`).  
- **IDs and classes:**  
  - IDs must be unique per page and used sparingly.
  - Classes should be meaningful (`.btn-primary` not `.blue-button`).  
- **Comments:** Use `<!-- -->` for sectioning, keep them concise.  

---

## 6. UI/UX Guidelines

- **Content-first:** Use HTML that makes sense even without CSS/JS.  
- **Responsive layout base:** Use `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.  
- **Media elements:**  
  - Always provide `<track>` captions in `<video>`.  
  - Provide fallback text inside `<audio>`.  

---

## 7. Non-Functional Requirements  

- **Accessibility:** Minimum WCAG 2.1 Level AA compliance while striving for AAA when possible.
- **Performance:** HTML files should be lean (minify in production).  
- **SEO:** Use semantic tags, meta descriptions, Open Graph tags.  

---

## 8. Tooling & Environment

- **Validation:** Use [W3C HTML Validator](https://validator.w3.org/).  
- **Linters:** HTMLHint or built-in IDE extensions.  
- **Editor Settings:** Enforce auto-formatting (Prettier, EditorConfig).  

---

## 9. Governance & Review

- **Code Review:** Validate semantic correctness and accessibility compliance.  
- **Documentation:** Each HTML template/component should include comments explaining its role.  
- **Change Management:** Updates to guidelines require review by lead developer/architect.  

---

## 10. Appendices  

### Example Base HTML Template  

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content="Frontend Development Guide Example" />
    <title>Sample Page</title>
  </head>
  <body>
    <header>
      <h1>Frontend Development Guide</h1>
      <nav>
        <ul>
          <li><a href="#principles">Principles</a></li>
          <li><a href="#patterns">Patterns</a></li>
        </ul>
      </nav>
    </header>
    <main id="content">
      <section id="principles">
        <h2>Design Principles</h2>
        <p>Use semantic HTML for clarity and accessibility.</p>
      </section>
      <section id="patterns">
        <h2>Common Patterns</h2>
        <article>
          <h3>Forms</h3>
          <form>
            <label for="email">Email:</label>
            <input id="email" type="email" name="email" required />
            <button type="submit">Submit</button>
          </form>
        </article>
      </section>
    </main>
    <footer>
      <p>&copy; 2025 Frontend Team</p>
    </footer>
  </body>
</html>
```
