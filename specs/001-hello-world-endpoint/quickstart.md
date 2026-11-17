# Quickstart Validation Guide: Hello World Endpoint

**Date**: 2026-11-17

Use this guide to verify the feature works end-to-end after implementation.

---

## Prerequisites

- Python 3.12 installed
- Dependencies installed: `pip install -r requirements.txt`

---

## Run the tests (primary validation)

```bash
pytest tests/test_health.py -v
```

**Expected output** (all pass):

```
tests/test_health.py::test_health_returns_200 PASSED
tests/test_health.py::test_health_response_body PASSED
tests/test_health.py::test_health_post_returns_405 PASSED
```

---

## Run the server manually (optional smoke-test)

```bash
uvicorn main:app --reload
```

Then in a second terminal:

```bash
curl -s http://localhost:8000/v1/health
```

**Expected response**:

```json
{"status": "Hello, World!"}
```

**Verify 405 on POST**:

```bash
curl -s -o /dev/null -w "%{http_code}" -X POST http://localhost:8000/v1/health
```

Expected: `405`

---

## Validation checklist

- [ ] `pytest` exits with code 0 and all tests pass
- [ ] `GET /v1/health` returns HTTP 200 with `{"status": "Hello, World!"}`
- [ ] `POST /v1/health` returns HTTP 405
- [ ] Response `Content-Type` header is `application/json`

See [contracts/health.md](contracts/health.md) for the full HTTP contract and
[data-model.md](data-model.md) for the response schema.
