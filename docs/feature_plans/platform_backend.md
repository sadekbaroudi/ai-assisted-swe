# Platform Backend Foundation Implementation Plan (Identifier: !!platform_backend)

## Overview

The Platform Backend foundational feature (identifier: !!platform_backend) establishes the initial Laravel 10+ API project skeleton with core infrastructure needed for subsequent feature development. It covers routing (`/api/v1`), layered architecture scaffolding, base database schema (users, groups, events, polls), exception handling envelope, authentication bootstrap (JWT stubs), middleware, logging, OpenAPI stub, health/readiness endpoints, and environment configuration.

This plan defines concrete implementation steps, directory structures, interfaces, and testing strategy to satisfy every checklist item under !!platform_backend. It purposefully limits scope to scaffolding and stubs (no full business logic) while ensuring extensibility and SOLID compliance.

## Requirements Mapping

- [ ] Laravel 10+ project initialized
- [ ] `/api/v1` route group with REST resource conventions (placeholder controllers)
- [ ] MySQL 8 connection configured
- [ ] Base migrations: users, groups, events, polls (skeleton columns only)
- [ ] Layered architecture directories: Controllers, Services, Repositories, DTOs, Policies, Middleware
- [ ] Central exception handler returning uniform JSON envelope `{ data, meta, errors }`
- [ ] JWT auth bootstrap: stub controllers for login, refresh, revoke (no full logic yet)
- [ ] Core middleware registered: `auth:api`, rate limiting, request ID, force JSON, CORS
- [ ] Logging stub: Monolog channel, env-driven `LOG_PATH` / `LOG_LEVEL`, directory auto-create
- [ ] OpenAPI stub: `/api/docs` route serving placeholder spec & script hook for future generation
- [ ] Health & readiness endpoints: `/health` (liveness), `/ready` (DB + queue check simulation)
- [ ] `.env.example` with required base variables (DB, JWT, LOG_PATH, APP_URL)

## Architectural Notes

- Layered approach: Controller -> Service -> Repository -> (Model / DB). DTOs isolate outbound/inbound shapes. Policies reserved for future authorization logic.
- Dependency Inversion: Controllers type-hint service interfaces; services depend on repository interfaces; bind concrete implementations in a Service Provider.
- Logging & Exception Handling centralization ensures consistent envelope and structured logs early.
- JWT: Use `firebase/php-jwt` (placeholder) or `lcobucci/jwt` - select `firebase/php-jwt` for lighter bootstrap. Only scaffolding token issue/refresh signature; real user verification deferred to later authentication feature set.

## Data Model Skeletons

Migrations (minimal columns deliberately — full semantics added in later feature plans):

1. users: id (PK), name, email (unique), password (nullable until auth done), created_at, updated_at
2. groups: id, name, description (nullable), created_at, updated_at
3. events: id, group_id (FK), title, description (nullable), status (enum planning/confirmed/completed/cancelled placeholder), host_user_id (FK), event_date (nullable datetime), created_at, updated_at
4. polls: id, event_id (FK), title (nullable), status (enum active/finalized), created_at, updated_at
5. poll_options (supporting for forward compatibility): id, poll_id (FK), option_datetime (datetime), created_at, updated_at
6. poll_votes: id, poll_option_id (FK), user_id (FK), created_at, updated_at

(Poll subsidiary tables added because future features rely on them; marking them optional if strict scope prefers only a single polls table.)

- [ ] Confirm whether to include poll_options & poll_votes now (Assumption: include to avoid migration churn later.)

## Directory Structure (Proposed)

```
backend/
  app/
    Http/
      Controllers/Api/V1/
      Middleware/
    Services/
      Contracts/
    Repositories/
      Contracts/
    DTOs/
    Policies/
    Providers/
    Exceptions/
  routes/
    api.php
  config/
    logging.php (extended)
    openapi.php (stub)
  database/
    migrations/
  resources/
    openapi/placeholder.yaml
  tests/
    Feature/
    Unit/
```

## Implementation Steps

### 1. Project Initialization
- [ ] Create Laravel 10 project under `backend/`
- [ ] Add Composer dependencies: `firebase/php-jwt`, `ramsey/uuid` (for request IDs), `doctrine/dbal` (future migrations flexibility)
- [ ] Configure PSR-4 namespaces for new folders (DTOs, Services, Repositories)

### 2. Environment & Config
- [ ] Create `.env.example` with: `APP_NAME, APP_ENV, APP_KEY (placeholder), APP_URL, LOG_CHANNEL, LOG_PATH, LOG_LEVEL, DB_HOST, DB_PORT, DB_DATABASE, DB_USERNAME, DB_PASSWORD, JWT_SECRET, JWT_TTL, JWT_REFRESH_TTL`
- [ ] Add custom config `request.php` (optional) for request ID header constant
- [ ] Update `config/logging.php` to include custom channel using env path & level
- [ ] Bootstrap log directory creation in a service provider

### 3. Migrations & Models
- [ ] Generate migrations for users, groups, events, polls, poll_options, poll_votes
- [ ] Define minimal Eloquent models referencing relationships (stubs only)
- [ ] Run migration locally (CI later) to validate schema

### 4. Layered Structure Scaffolding
- [ ] Create base interfaces: `UserRepositoryInterface`, `GroupRepositoryInterface`, etc. (empty or minimal method signatures for now)
- [ ] Implement simple Eloquent repositories with placeholder methods (e.g., `findById`) returning models or null
- [ ] Create service interfaces & concrete classes wrapping repositories (e.g., `UserServiceInterface` / `UserService`)
- [ ] Bind interfaces in `AppServiceProvider` or dedicated `RepositoryServiceProvider`

### 5. Routing & Controllers
- [ ] Define `/api/v1` route group prefix & name spacing in `routes/api.php`
- [ ] Add placeholder REST resource routes for users, groups, events, polls (index/show/store/update/destroy) referencing stub controllers returning placeholder JSON `{ data: [] }`

### 6. Middleware
- [ ] Implement RequestIdMiddleware: generate UUID v4, attach to response header `X-Request-ID` & log context
- [ ] ForceJsonMiddleware: ensure `Accept: application/json` or set it; enforce JSON responses
- [ ] Register CORS (use Laravel default `HandleCors`), Rate Limiter (configure in `RouteServiceProvider` or `Fortify`-style), and `auth:api` guard placeholder

### 7. Authentication Bootstrap
- [ ] Add `AuthController` with methods: `login`, `refresh`, `revoke` returning placeholder tokens (dummy encoded JWT using secret & short payload)
- [ ] Configure `config/auth.php` guard `api` using `jwt` (custom driver stub) or leave guard as `sanctum` placeholder but document future swap (Decision: implement lightweight custom JWT helper class now)
- [ ] Create `JwtService` with methods: `issueAccessToken($userId)`, `issueRefreshToken($userId)`, `validate($token)` (stub logic returning decoded payload or error)

### 8. Central Exception Handling
- [ ] Extend `Handler` to format all JSON responses as `{ data, meta, errors }`
- [ ] Create helper for envelope building & unify success responses in a base controller trait
- [ ] Map common exceptions (ModelNotFound, ValidationException, AuthenticationException) to consistent HTTP status & envelope structure

### 9. Logging Infrastructure
- [ ] Custom Monolog channel with StreamHandler pointing to `LOG_PATH/app.log`
- [ ] Ensure directory creation if missing
- [ ] Add contextual processors: request ID, user ID (if authenticated), memory usage
- [ ] Add simple test to assert log file writable

### 10. OpenAPI Stub
- [ ] Add route `/api/docs` serving static Swagger UI (use simple Blade or plain HTML referencing CDN) & placeholder spec YAML
- [ ] Provide `resources/openapi/placeholder.yaml` with meta info & at least one path (`/health`)
- [ ] Add npm script hook placeholder (documented) for future automated generation (deferred actual tooling)

### 11. Health & Readiness Endpoints
- [ ] `/health` returns `{ data: { status: 'ok', time: <iso> }, meta: {}, errors: [] }`
- [ ] `/ready` attempts DB connection & (stub queue check) returning readiness state; include reasons if failing

### 12. Testing
- [ ] Unit tests: JwtService issuance & validation (happy path, invalid token)
- [ ] Feature tests: health endpoint 200, ready endpoint 200 (with migrated DB), login returns envelope
- [ ] Feature route tests for resource placeholders (index returns envelope)
- [ ] Middleware tests: Request ID present, Force JSON sets header
- [ ] Exception test: hitting unknown model route returns standardized error envelope

### 13. CI Considerations (Scaffold Only)
- [ ] Add GitHub Actions workflow stub (if not existing) to run `composer install`, `php artisan migrate --env=testing`, and `phpunit` (May be separate feature but referenced here for foundation) 
- [ ] Mark as partial if broader CI feature has its own plan

### 14. Documentation
- [ ] Update root `README.md` (or create) with backend setup instructions (composer install, env copy, key:generate, migrate, run server, health endpoints)
- [ ] Document envelope format & placeholder auth endpoints
- [ ] Document directory structure rationale

## Testing Strategy Details

Unit Tests
- [ ] JwtService: token generation includes exp & sub claims
- [ ] JwtService: invalid signature returns error

Feature Tests
- [ ] GET /api/v1/health returns 200 schema
- [ ] GET /api/v1/ready with DB migrated returns ready true
- [ ] POST /api/v1/auth/login returns access & refresh token placeholders
- [ ] GET protected route without token returns 401 envelope
- [ ] Resource controller index returns empty array data

Middleware Tests
- [ ] RequestIdMiddleware adds X-Request-ID & logs line contains ID
- [ ] ForceJsonMiddleware sets Accept header and response Content-Type

Exception Formatting Tests
- [ ] Trigger validation error (e.g., POST with missing field) returns envelope with errors array
- [ ] ModelNotFound triggers 404 envelope

OpenAPI Stub
- [ ] GET /api/docs serves HTML containing 'Swagger UI'

Logging
- [ ] After hitting a route, `storage/logs/app.log` file exists & contains request ID

## Manual Testing Checklist
- [ ] Start server, hit /api/v1/health
- [ ] Hit /api/v1/ready after stopping DB service to see failure response
- [ ] Call login & use access token on a protected placeholder route
- [ ] Inspect /api/docs in browser (user-run) & verify placeholder spec content

## Risks & Mitigations

- Scope creep into full auth logic → Keep stubs & mark TODO for real credential validation.
- Over-engineering repositories early → Keep minimal method set (find, create) until needed.
- Token security incomplete → Explicit warnings in comments & README.

## Future Extensions (Outside Current Scope)

- Full auth & password hashing
- Email verification & refresh token persistence store
- Dynamic OpenAPI generation (e.g., `darkaonline/l5-swagger` or `knuckleswtf/scribe`)
- Structured logging to JSON & shipping to ELK / OpenTelemetry

## Completion Definition

Feature considered complete when all requirement checkboxes above are checked, tests pass, README updated, and health + docs endpoints function with consistent JSON envelope.

---

Status: DRAFT
