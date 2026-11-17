# Research: Hello World Endpoint

**Date**: 2026-11-17

All technical decisions are fully determined by the project constitution (Python 3.12, FastAPI,
PostgreSQL, pytest). No external research was required. Findings below document the chosen
patterns for implementer reference.

---

## Decision: FastAPI router organisation

**Decision**: Single `APIRouter` in `app/routers/health.py`, included in `main.py` with the
`/v1` prefix.

**Rationale**: Keeps the app factory (`main.py`) clean and separates routing concerns from
application bootstrap without introducing unnecessary abstraction. This pattern scales to
multiple routers without restructuring.

**Alternatives considered**: Defining the route directly in `main.py` — rejected because it
conflates app bootstrap with business routes, making future additions noisier.

---

## Decision: Pydantic v2 response model

**Decision**: Define a `HealthResponse(BaseModel)` with a single `status: str` field.
Annotate the endpoint with `response_model=HealthResponse`.

**Rationale**: Constitution Principle II requires all response schemas to be Pydantic v2
models. A typed model also auto-generates the OpenAPI schema for free.

**Alternatives considered**: Returning a plain `dict` — rejected; violates Principle II
(no `Any` / untyped returns).

---

## Decision: Testing approach

**Decision**: Use FastAPI's `TestClient` (backed by `httpx`) in `tests/test_health.py`.
Run with `pytest`.

**Rationale**: Constitution Principle IV requires pytest. `TestClient` lets tests exercise
the full ASGI stack without a live server and without mocking. No database fixture is needed
because this endpoint performs no database operations.

**Alternatives considered**: `requests` against a live server — rejected; requires a running
process and is harder to integrate with CI.

---

## Decision: HTTP method rejection

**Decision**: FastAPI automatically returns 405 for unregistered methods on a known path.
No explicit handler needed.

**Rationale**: Simplicity (Principle V). FastAPI's default behaviour satisfies FR-004.

**Alternatives considered**: Explicit `@app.post`, `@app.put` etc. returning 405 — rejected;
unnecessary boilerplate.
