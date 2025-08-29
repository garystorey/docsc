# Backend Development Guide (Express / Hono with TypeScript)

## 1. Introduction

**Purpose:**

Define standards and practices for building reliable, secure, and performant HTTP APIs with TypeScript using Express or Hono. Refer to [TypeScript](./TypeScript.md) for best practices.

**Scope:**

Covers project structure, coding standards, routing, middleware, validation, error handling, logging, security, performance, database access, testing, observability, and governance.

**Audience:**

Backend developers, reviewers, QA, and tech leads.

**References:**

- [Node.js (LTS)](https://nodejs.org/)
- [TypeScript](https://www.typescriptlang.org/)
- [Express](https://expressjs.com/)
- [Hono](https://hono.dev/)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [The Twelve-Factor App](https://12factor.net/)

---

## 2. Core Principles

- **Prefer TypeScript** (`.ts`) with `"strict": true`.
- **Stateless, idempotent services** where possible; side effects are explicit.
- **Single responsibility per module**; small, composable units.
- **Dependency boundaries:** route → handler → service → data access.
- **Fail fast in dev; fail gracefully in prod.**
- **Security first:** validate, sanitize, least privilege, no secrets in code.

**Recommended tools:**

- [TypeScript Compiler (`tsc`)](https://www.typescriptlang.org/docs/handbook/compiler-options.html) with strict options  
- [Node 18+](https://nodejs.org/en/download) (built-in [`fetch`/`undici`](https://undici.nodejs.org/), [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController))

---

## 3. Project Structure

- `src/` → application code (feature- or domain-oriented)
- `src/server.ts` → bootstrap/HTTP wiring (no business logic)
- `src/routes/` → route definitions per resource
- `src/handlers/` → thin request handlers (parse → call service → shape response)
- `src/services/` → business logic; framework-agnostic
- `src/middleware/` → auth, CORS, rate limit, logging, error mapping
- `src/db/` → ORM/queries, migrations, repositories
- `src/utils/` → pure utilities (no I/O, no globals)
- `src/config/` → environment parsing, constants, feature flags
- `src/types/` → shared types, DTOs
- `tests/` → unit, integration, e2e mirroring `src`

**Recommended tools:**

- [`tsconfig` path aliases](https://www.typescriptlang.org/tsconfig#paths) (+ [tsconfig-paths](https://github.com/TypeStrong/ts-node#paths-and-baseurl))  
- [.editorconfig](https://editorconfig.org/)

---

## 4. Coding Standards

- Use `const` by default; `let` when needed; never `var`.
- Strict equality (`===`, `!==`); no implicit truthiness for guards.
- No `any` in production code; use `unknown` + narrowing.
- Pure utilities in `utils/` remain side-effect free.
- Avoid global singletons except injected infra (logger, DB pool).
- Prefer early returns over nested `if`/`elif`/`else` blocks.
- Prefer validation libraries over custom validations unless necesary.

**Recommended tools:**

- [ESLint](https://eslint.org/) + [typescript-eslint](https://typescript-eslint.io/), [eslint-plugin-import](https://github.com/import-js/eslint-plugin-import), [eslint-plugin-security](https://github.com/nodesecurity/eslint-plugin-security)
- [Prettier](https://prettier.io/)
- [Husky](https://typicode.github.io/husky/) + [lint-staged](https://github.com/okonet/lint-staged)

---

## 5. Routing & Middleware

- **Express:** register security/logging/body-parse → routers → 404 → error handler.
- **Hono:** compose middlewares; leverage `Context` helpers.
- Versioned resource routes: `/v1/users`, `/v1/sessions`.
- Handlers are thin: validate → service → response mapping.

**Recommended tools:**

- Express: [express](https://expressjs.com/), [express-async-errors](https://github.com/davidbanham/express-async-errors)  
- Parsers/Security: [express.json](https://expressjs.com/en/api.html#express.json), [helmet](https://helmetjs.github.io/), [cors](https://github.com/expressjs/cors)  
- Hono: [hono](https://hono.dev/), [@hono/cors](https://hono.dev/middleware/builtin/cors), [@hono/helmet](https://hono.dev/middleware/builtin/helmet)

---

## 6. Validation & Data Shaping

- Validate every boundary: params, query, body, headers, env.
- Use schema validation; infer types from schemas.
- Map raw request data to typed DTOs; reject unknown properties.

**Recommended tools:**

- [Zod](https://zod.dev/) (primary) • Alternatives: [Valibot](https://valibot.dev/), [ArkType](https://arktype.io/)  
- API docs generators: [zod-openapi](https://github.com/asteasolutions/zod-to-openapi), [zod-to-json-schema](https://github.com/StefanTerdell/zod-to-json-schema)  

---

## 7. Error Handling & Response Model

- Central error middleware maps domain errors → HTTP codes.
- Standard JSON envelope: `code`, `message`, `details?`, `requestId`.
- No stack traces in prod responses; full details logged only.

**Recommended tools:**

- Envelope helpers or [http-errors](https://github.com/jshttp/http-errors) (optional)  
- Monitoring: [Sentry](https://sentry.io/welcome/) or [Honeybadger](https://www.honeybadger.io/)

---

## 8. Logging & Correlation

- Structured JSON logs; one info log per request; errors at error level.
- Include `requestId` in logs and responses; propagate/assign if missing.

**Recommended tools:**

- [Pino](https://getpino.io/) • [pino-http](https://github.com/pinojs/pino-http) • [pino-pretty](https://github.com/pinojs/pino-pretty)  
- Request ID: [express-request-id](https://github.com/floatdrop/express-request-id) (Express) or custom middleware (Hono)  
- Shippers: [Vector](https://vector.dev/), [Fluent Bit](https://fluentbit.io/) • Backends: [Google Cloud Logging](https://cloud.google.com/logging), [Datadog](https://www.datadoghq.com/), [Elastic Stack](https://www.elastic.co/)

---

## 9. Security

- TLS everywhere; secure headers; strict CORS.
- Short-lived tokens; rotate refresh; verify JWTs with JWKs.
- RBAC/ABAC in services; sanitize inputs; no eval/dynamic code.
- Secrets from env/secret manager; never in VCS.
- Rate limit sensitive routes; safe file uploads.

**Recommended tools:**

- Headers/CORS: [helmet](https://helmetjs.github.io/) (Express), [@hono/helmet](https://hono.dev/middleware/builtin/helmet), [cors](https://github.com/expressjs/cors)  
- JWT & crypto: [`jose`](https://github.com/panva/jose)  
- Password hashing: [argon2](https://github.com/ranisalt/node-argon2) (preferred) or [bcrypt](https://github.com/kelektiv/node.bcrypt.js)  
- Rate limiting: [rate-limiter-flexible](https://github.com/animir/node-rate-limiter-flexible)  
- File uploads: [multer](https://github.com/expressjs/multer) (Express) • [busboy](https://github.com/mscdex/busboy) • [formidable](https://github.com/node-formidable/formidable)  
- Secrets management: [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/), [GCP Secret Manager](https://cloud.google.com/secret-manager), [HashiCorp Vault](https://www.vaultproject.io/) • Local dev: [dotenv](https://github.com/motdotla/dotenv)

---

## 10. Performance & Reliability

- Keep-alive & connection pooling; compress where appropriate.
- Proper caching (`ETag`, `Cache-Control`), conditional requests.
- Idempotency for unsafe ops; queues for long-running work.
- Timeouts & retries with backoff + jitter; avoid retrying non-idempotent ops.

**Recommended tools:**

- Compression: [compression](https://github.com/expressjs/compression) (Express)  
- Caching: [Redis](https://redis.io/) clients ([ioredis](https://github.com/redis/ioredis)); in-process [lru-cache](https://github.com/isaacs/node-lru-cache)  
- Retries/Timeouts: [p-retry](https://github.com/sindresorhus/p-retry), [p-timeout](https://github.com/sindresorhus/p-timeout)  
- Circuit breaker: [opossum](https://github.com/nodeshift/opossum)  
- Queues/Jobs: [BullMQ](https://docs.bullmq.io/), or cloud-native [Amazon SQS](https://aws.amazon.com/sqs/) / [Google Cloud Tasks](https://cloud.google.com/tasks)

---

## 11. Async, Cancellation, and Timeouts

- Use `AbortController`/`AbortSignal` end-to-end for cancellable work.
- Apply per-operation timeouts; propagate aborts to downstream calls.
- Distinguish timeout vs. abort vs. system/domain errors.

**Recommended tools:**

- Node 18+ [`fetch`/`undici`](https://undici.nodejs.org/#/docs/api/Fetch-API) with `AbortSignal`  
- Promise wrappers: [p-timeout](https://github.com/sindresorhus/p-timeout)

---

## 12. Database & Data Access

- Migrations are reviewed and immutable post-merge.
- Parameterized queries only; repositories hide SQL details.
- Transactions for multi-step mutations; correct isolation levels.
- Handle concurrency (optimistic or pessimistic as appropriate).
- Least-privilege DB roles; backups and restore drills.

**Recommended tools:**

- ORM/Query: [Prisma](https://www.prisma.io/), [Drizzle ORM](https://orm.drizzle.team/), [Kysely](https://kysely.dev/)  
- Migrations: [Prisma Migrate](https://www.prisma.io/docs/concepts/components/prisma-migrate), [Drizzle Kit](https://orm.drizzle.team/docs/overview), [Knex](https://knexjs.org/)  
- Drivers: [pg](https://github.com/brianc/node-postgres), [mysql2](https://github.com/sidorares/node-mysql2), [better-sqlite3](https://github.com/WiseLibs/better-sqlite3)  

---

## 13. API Design

- RESTful resource naming; version via `/v1`; no breaking changes in version.
- Cursor-based pagination preferred; explicit filter/sort allow-lists.
- JSON casing (camelCase); timestamps ISO 8601 UTC.

**Recommended tools:**

- API specs: [OpenAPI 3](https://spec.openapis.org/oas/latest.html)  
- Generators: [openapi-typescript](https://github.com/drwpow/openapi-typescript), [zod-openapi](https://github.com/asteasolutions/zod-to-openapi)  
- Docs UI: [Redoc](https://redocly.com/redoc/), [Swagger UI](https://swagger.io/tools/swagger-ui/)

---

## 14. Observability & Health

- Health endpoints: `/healthz` (liveness), `/readyz` (readiness).
- Metrics and tracing for requests, DB, queues; actionable alerts.

**Recommended tools:**

- [OpenTelemetry](https://opentelemetry.io/) (SDK + auto-instrumentations; OTLP exporters)  
- Metrics: [prom-client](https://github.com/siimon/prom-client), [express-prom-bundle](https://github.com/joao-fontenele/express-prom-bundle)  
- Health: [@godaddy/terminus](https://github.com/godaddy/terminus) (Express), lightweight custom in Hono  

---

## 15. Testing Strategy

- Unit tests for utils/services/validation.
- Integration tests for handlers/middleware.
- E2E tests for critical flows against a real server.
- Isolated, repeatable test data; DB reset between tests.

**Recommended tools:**

- Runners: [Vitest](https://vitest.dev/) or [Jest](https://jestjs.io/)  
- HTTP testing: [Supertest](https://github.com/ladjs/supertest) (Express) • Hono’s [testing utils](https://hono.dev/docs/guides/testing) or Supertest via Node server  
- API mocking: [MSW](https://mswjs.io/)  
- Containers: [Testcontainers](https://testcontainers.com/)  
- E2E: [Playwright](https://playwright.dev/)  
- Coverage: [c8](https://github.com/bcoe/c8)

---

## 16. Configuration & Environments

- Parse env in a single module; validate on boot; fail fast on invalid/missing.
- Use env vars for differences across environments; no per-env code branches.
- Typed feature flags are centrally registered.

**Recommended tools:**

- Env validation: [Zod](https://zod.dev/)
- Env loading (local only): [dotenv](https://github.com/motdotla/dotenv), [dotenv-flow](https://github.com/kerimdzhanov/dotenv-flow)  
- Feature flags: [LaunchDarkly](https://launchdarkly.com/), [ConfigCat](https://configcat.com/), [OpenFeature](https://openfeature.dev/)

---

## 17. Build, CI/CD, and Operations

- Reproducible builds; pinned lockfile; minimal images; non-root runtime.
- CI: typecheck, lint, test, vulnerability scan, size checks.
- DB migrations run on deploy; health checks gate rollout.
- Automated dependency updates with review.

**Recommended tools:**

- Build: [tsup](https://tsup.egoist.dev/), [esbuild](https://esbuild.github.io/), [SWC](https://swc.rs/) • Dev runner: [tsx](https://github.com/esbuild-kit/tsx)  
- Process manager: [PM2](https://pm2.keymetrics.io/) (if not on serverless/managed)  
- CI: [GitHub Actions](https://github.com/features/actions) / [GitLab CI](https://docs.gitlab.com/ee/ci/)  
- Containers: [Docker](https://www.docker.com/) (multi-stage), [Docker Compose](https://docs.docker.com/compose/) for local  
- Vulnerabilities: [`npm audit`](https://docs.npmjs.com/cli/v10/commands/npm-audit), [Trivy](https://aquasecurity.github.io/trivy/), [Snyk](https://snyk.io/)  
- Dependency updates: [Renovate](https://www.mend.io/renovate/), [Dependabot](https://docs.github.com/en/code-security/dependabot)  
- DAST (optional): [OWASP ZAP](https://www.zaproxy.org/)

---

## 18. Governance & Review

- PRs must pass CI and include tests for new behavior.
- New dependencies require justification (size, maintenance, security).
- Security-sensitive changes receive dedicated review.
- Breaking API changes require versioning plan and consumer communication.
