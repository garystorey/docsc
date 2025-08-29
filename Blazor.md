# Blazor Web Application Architecture Summary

Only use Blazor if required by the client. Otherwise choose a different framework.
MS doesnt even use it.

## Architectural Principles

| Principle                          | Decision                                                                 |
|-----------------------------------|--------------------------------------------------------------------------|
| **Data Flow**                     | One-way: data flows down, events bubble up                               |
| **State Management**              | Parent components hold and manage state                                  |
| **Component Architecture**        | Stateless, reusable components using EventCallback                        |
| **Folder Organization**           | Feature-based + shared structure           |
| **UI Framework**                  | Custom or MS Fabric UI                     |
| **Form Binding**                  | EditForm with validation        |
| **API Communication**             | Abstracted via service interfaces (e.g., IUserService) using HttpClient with cancellation support |
| **Error & Feedback Handling**     | ApiResponse wrapper |
| **Layout, Theming, and Styling**  | Semantic HTML layout + MS Fabric UI layout primitives, SCSS-based custom styles, and theme support |
| **DTO Organization**              | DTOs isolated under `/Models/DTOs` per Clean Architecture                |

---

## Folder Structure

```text
/
├── /wwwroot
│   ├── /css  
│   |   └── global.(s)css
│   └── /js
│       ├── global.js
│       └── simpleFunction.js
├── /UI
|   ├── /Pages
│   |   ├── UserPage.razor
│   |   └── Index.razor
|   ├── /Layouts
│   |   ├── MainLayout.razor
|   ├── /Themes
│   |   ├── ThemeProvider.razor
│   |   ├── CASTheme-Red.cs
|   |   └── CASTheme-Blue.cs
|   ├── /Components
│   |   └── NavMenu.razor
│   |   └── Card.razor
│   |   ├── List.razor
│   |   ├── Search.razor
|   └── /Features
│   |   └── /Users
│   |       ├── EditUser.razor
│   |       ├── UserCard.razor
│   |       ├── UserValidator.cs
│   |       └── UserViewModel.cs
|   └── _Imports.razor
|   └── App.razor
|   └── Routes.razor
├── /Models
│   ├── /DTOs
│   │   └── UserDto.cs
│   └── ApiResponse.cs
├── /Services
│   ├── IUserService.cs
│   └── UserService.cs
├── /Utils
│   └── StringUtils.cs
└── Program.cs
└── appsettings.json

```

**Please Note**: By default, Blazor app will create a (*inappropriately*) named `Components` folder at the root level.  In this case, we have renamed this folder to `UI` and updated the namespace.

---

## Key Implementation Decisions

### 1. **DTO / ViewModel**

- DTOs (e.g., `UserDto`) are stored in `/Models/DTOs` and used for API transport.
- ViewModels (e.g., `UserViewModel`) are created specifically for form binding with validation attributes.

### 2. **Service Abstraction**

- All application services follow the interface-implementation pattern.
- Services such as `UserService` implement their corresponding interface (e.g., `IUserService`).
- Interfaces and implementations are registered with dependency injection (DI) in `Program.cs`.
- This promotes testability, maintainability, and separation of concerns.

### 3. **Form Handling**

- All forms use `EditForm`.
- All required fields should be validated.
- All form fields should have correctly associated labels, descriptions and errors.

### 4. **Error Handling & Feedback**

- API responses are wrapped in a generic `ApiResponse` model.
- A Snackbar component is used for user-facing feedback messages on success/failure.

### 5. **Async Safety**

- `CancellationTokenSource` is used in components for lifecycle-safe async operations.
- Long-running or cancelable methods in services accept `CancellationToken` parameters.

### 6. **Theming and Styling**

- Theme based global styling if available.
- (S)CSS is used for custom styles.
- No inline styles should be used.
- No `!important` rules. Use modern CSS scoping features.
- Prefer modern CSS syntax.

### 7. **Parent-Child Component Pattern**

- Components follow a clean structure:
  - **Parent** owns state and passes data down to children
  - **Child** render data and emits events
- This encourages separation of concerns, testability, and reusability.

### 8. **Testing Strategy**

- Unit testing will be used to validate service logic, ViewModels, and utility classes.
- Component testing will be done using [bUnit](https://bunit.dev/) to ensure Razor components behave correctly in isolation.
- **Why bUnit was chosen:**
  - Specifically designed for Blazor component testing
  - Supports full Razor syntax, lifecycle hooks, and parameter binding
  - Enables simulation of event callbacks and user interactions
  - Seamlessly integrates with xUnit, NUnit, and MSTest
  - Compatible with MudBlazor

- End-to-End (E2E) testing will be performed using [Playwright](https://playwright.dev/):
  - Tests real browser-based user flows in the running Blazor InteractiveServer app
  - Ideal for testing navigation, form validation, UI responsiveness, authentication, and accessibility
  - Works well alongside bUnit by focusing on full app behavior

- Test coverage includes:
  - Services (e.g., `IUserService`) with mocked `HttpClient`
  - ViewModel logic with validation scenarios
  - Event-driven component communication (e.g., `EventCallback` triggers)
  - UI rendering and interactions with bUnit and MudBlazor integration
  - Full user flows and browser interaction using Playwright

- Tests will be placed in a parallel test project (e.g., `/Client.Tests`) following the same folder structure as `/Client`.

### 9. **HTML/CSS Standards**

- Use semantic markup.  
  - Use tags with landmark roles for layouts (`header`, `footer`, `main`, `aside`, `nav`, etc.).
  - Use correct Heading levels to create a document outline
    - Headings go in order (`h1` -> `h2` -> `h3`)
    - Use CSS to control look and feel.
  - Use the most appropriate HTML tag for the use case.
  - Buttons perform actions, links go to places
    - Buttons and links should always have text descriptions
- Accessibility best practices
  - Form fields have associated labels and descriptions
  - Text has proper color contrast
  - Passes standard a11y tests
- Styles should be scoped at a component level.
  - Global styles should be limited.
  - No inline styles
  - No `!important`
- Code should be inside `@code` blocks not in the component tree

---
