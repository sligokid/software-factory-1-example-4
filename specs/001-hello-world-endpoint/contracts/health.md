# Contract: GET /v1/health

**Version**: 1.0 | **Date**: 2026-11-17

---

## Endpoint

| Property | Value |
|----------|-------|
| Method | `GET` |
| Path | `/v1/health` |
| Auth | None |
| Content-Type (response) | `application/json` |

---

## Success Response — 200 OK

```json
{
  "status": "Hello, World!"
}
```

| Field | Type | Always present | Description |
|-------|------|---------------|-------------|
| `status` | string | Yes | Greeting message |

---

## Error Responses

| Status | Trigger | Body |
|--------|---------|------|
| 405 Method Not Allowed | Any method other than GET | FastAPI default error body |
| 406 Not Acceptable | Client requests unsupported `Accept` type | FastAPI default error body |
| 503 Service Unavailable | Service not yet fully initialised | No body guaranteed |

---

## Notes

- No request body, query parameters, or headers are required.
- The `status` field value is fixed at `"Hello, World!"` for v1.
- This contract is stable; breaking changes require a new version prefix (e.g. `/v2/health`).
