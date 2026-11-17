# Tasks: Hello World Endpoint

**Input**: Design documents from `specs/001-hello-world-endpoint/`

**Prerequisites**: plan.md ✓, spec.md ✓, research.md ✓, data-model.md ✓, contracts/ ✓

**Tests**: Included — constitution Principle IV requires test-first development with pytest.

**Organization**: Tasks grouped by phase (one phase = one commit). Single user story (US1).

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to ([US1])

---

## Phase 1: Setup

**Purpose**: Create the project skeleton and declare all dependencies.

**Commit scope**: `requirements.txt` + package structure only (no application logic).

- [ ] T001 Create `requirements.txt` with dependencies: `fastapi`, `uvicorn[standard]`, `pytest`, `httpx`
- [ ] T002 [P] Create `app/__init__.py` (empty — marks `app/` as a package)
- [ ] T003 [P] Create `app/routers/__init__.py` (empty — marks `app/routers/` as a package)
- [ ] T004 Create `tests/test_placeholder.py` with `def test_placeholder(): pass` so `pytest` exits 0 before real tests exist (may be removed in Phase 3)

**Checkpoint**: `pip install -r requirements.txt` succeeds; `pytest` exits 0.

---

## Phase 2: User Story 1 — Verify API Is Running (Priority: P1) 🎯 MVP

**Goal**: Expose `GET /v1/health` returning `{"status": "Hello, World!"}` with no authentication.

**Independent Test**: Run `pytest tests/test_health.py -v` — all three test cases must pass.

### Tests for User Story 1 (write first — must FAIL before implementation)

- [ ] T005 [US1] Write `test_health_returns_200` in `tests/test_health.py`: assert `GET /v1/health` returns HTTP 200
- [ ] T006 [US1] Write `test_health_response_body` in `tests/test_health.py`: assert response JSON equals `{"status": "Hello, World!"}`
- [ ] T007 [US1] Write `test_health_post_returns_405` in `tests/test_health.py`: assert `POST /v1/health` returns HTTP 405
- [ ] T008 [US1] Verify new tests FAIL with `pytest tests/test_health.py` before any implementation exists

### Implementation for User Story 1

- [ ] T009 [US1] Create `HealthResponse` Pydantic v2 model in `app/routers/health.py` with field `status: str` (non-empty string, as per data-model.md)
- [ ] T010 [US1] Implement `GET /health` route in `app/routers/health.py` returning `HealthResponse(status="Hello, World!")` with `response_model=HealthResponse`
- [ ] T011 [US1] Create `main.py`: instantiate `FastAPI()` app and call `app.include_router(health_router, prefix="/v1")` — no other routes
- [ ] T012 [US1] Run `pytest tests/test_health.py -v` and confirm all three tests pass

**Checkpoint**: User Story 1 is fully functional. `pytest` exits 0. Manual smoke-test matches `quickstart.md`.

---

## Phase 3: Polish & Cross-Cutting Concerns

**Purpose**: Final validation and type-safety check.

- [ ] T013 Confirm all function signatures in `app/routers/health.py` and `main.py` carry complete type hints (constitution Principle II)
- [ ] T014 [P] Remove `tests/test_placeholder.py` and run `pytest` (full suite) to confirm exit code 0

---

## Dependencies & Execution Order

### Phase Dependencies

- **Phase 1 (Setup)**: No dependencies — start immediately
- **Phase 2 (US1)**: Depends on Phase 1 completion
- **Phase 3 (Polish)**: Depends on Phase 2 completion

### User Story Dependencies

- **User Story 1 (P1)**: Only story; no cross-story dependencies

### Within Phase 2

- T005–T008 (tests): write before any implementation; T008 must confirm failure before T009
- T009–T010 (`health.py`): can be written together; T010 depends on T009 (model must exist)
- T011 (`main.py`): depends on T010 (router must exist to include)
- T012 (verify): runs after T011

### Parallel Opportunities

- T002 and T003 (Phase 1) can run in parallel
- T006 and T007 follow T005 sequentially (same file)
- T013 and T014 (Phase 3) can run in parallel

---

## Parallel Example: Phase 1

```bash
# These two tasks touch different files — run simultaneously:
Task T002: "Create app/__init__.py"
Task T003: "Create app/routers/__init__.py"
```

## Parallel Example: Phase 2 tests

```bash
# T005-T007 write to the same file — execute sequentially:
Task T005: "Write test_health_returns_200 in tests/test_health.py"
Task T006: "Write test_health_response_body in tests/test_health.py"
Task T007: "Write test_health_post_returns_405 in tests/test_health.py"
```

---

## Implementation Strategy

### MVP (single story, single commit)

1. Complete Phase 1: Setup → commit
2. Complete Phase 2: US1 (tests → implementation → verify) → commit
3. Complete Phase 3: Polish → commit
4. **VALIDATE**: `pytest` passes, smoke-test matches `quickstart.md`

---

## Notes

- [P] tasks operate on different files — safe to parallelise
- Tests MUST fail before implementation (constitution Principle IV)
- All type hints MUST be present throughout (constitution Principle II)
- No database interaction in this endpoint — no DB fixture needed
- Refer to `contracts/health.md` for exact HTTP contract (status codes, field names)
- Refer to `data-model.md` for `HealthResponse` field constraints
