# Data Model: Hello World Endpoint

**Date**: 2026-11-17

This endpoint performs no database reads or writes. The only data entity is the HTTP response
schema.

---

## HealthResponse

**Purpose**: Typed response body returned by `GET /v1/health`.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `status` | `str` | non-empty | Greeting message, e.g. `"Hello, World!"` |

**Validation rules**:
- `status` MUST be a non-empty string.
- No other fields are required.

**State transitions**: N/A — read-only response object, no persistence.

**Relationships**: None — this model is self-contained.

---

## Notes

- PostgreSQL is not accessed by this endpoint. No migration or ORM model is needed.
- If a future endpoint needs database access, the project will add SQLAlchemy models in a
  separate `app/models/` directory.
