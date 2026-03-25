# Development Guidelines for Claude - Python

## Core Philosophy

TDD with strict static typing, functional patterns where practical, modern Python 3.12+ idioms. Small incremental changes, always working state.

## Quick Reference

- TDD: Write tests first. Test behavior, not implementation.
- Typing: `pyright` strict mode. No `Any` unless interfacing untyped libs.
- Immutability: Frozen dataclasses (`frozen=True, slots=True`), `NamedTuple`, tuples over lists.
- Structure: Flat > nested. Composition > inheritance. Small pure functions.
- Pydantic at trust boundaries, dataclasses for domain logic.
- Protocol over ABC for structural typing.

### Preferred Tools
- **Package Manager**: uv (never pip directly)
- **Web**: FastAPI (APIs), Django (full-stack)
- **Testing**: pytest + hypothesis (property-based)
- **Typing**: pyright strict mode
- **Linting**: ruff format + ruff check
- **ML/LLM**: PyTorch, transformers, anthropic SDK, pydantic for structured output

## Project Setup

```bash
uv init my-project && cd my-project
uv add --dev pytest pytest-cov ruff pyright hypothesis
```

### pyproject.toml
```toml
[project]
requires-python = ">=3.12"

[tool.pyright]
pythonVersion = "3.12"
typeCheckingMode = "strict"

[tool.ruff]
target-version = "py312"
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "ANN", "B", "SIM", "TCH"]

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--strict-markers --tb=short -q"
```

## Typing

Use PEP 695 syntax (3.12+). Built-in generics, `|` unions, no `typing` imports for basics.

```python
type UserId = int
type Result[T] = Ok[T] | Err[str]

class Repository[T]:
    def find(self, id: int) -> T | None: ...
    def save(self, entity: T) -> T: ...

@runtime_checkable
class Cacheable(Protocol):
    def cache_key(self) -> str: ...
```

### Boundaries vs Domain
```python
# Pydantic at API boundaries
class CreateUserRequest(BaseModel):
    model_config = {"strict": True}
    email: str = Field(pattern=r"^[\w.+-]+@[\w-]+\.[\w.]+$")
    name: str = Field(min_length=1, max_length=255)

# Frozen dataclasses for domain
@dataclass(frozen=True, slots=True)
class User:
    id: int
    email: str
    name: str
    status: UserStatus = UserStatus.ACTIVE
```

## Testing

Name test files for behavior, not classes. Use factory functions with `defaults | overrides`. Use `hypothesis` for property-based testing of invariants. Use `pytest.mark.anyio` for async tests with `httpx.AsyncClient`.

```python
def make_payment(**overrides) -> Payment:
    defaults = {
        "amount": Decimal("100.00"),
        "currency": Currency.GBP,
        "status": PaymentStatus.PENDING,
    }
    return Payment(**(defaults | overrides))

class TestPaymentAuthorization:
    def test_pending_payment_can_be_authorized(self) -> None:
        payment = make_payment(status=PaymentStatus.PENDING)
        result = payment.authorize()
        assert result.status == PaymentStatus.AUTHORIZED

    def test_captured_payment_cannot_be_authorized(self) -> None:
        payment = make_payment(status=PaymentStatus.CAPTURED)
        with pytest.raises(InvalidTransitionError):
            payment.authorize()
```

## Architecture

- **FastAPI**: Use `Depends()` for DI. Keep route handlers thin, delegate to services.
- **Django**: Service layer pattern — views validate and delegate, services own logic.
- **ML/LLM**: Pydantic models for structured LLM output. Type all tensor operations.
- **Errors**: Use `Result[T, E] = Ok[T] | Err[E]` pattern for recoverable errors, exceptions for unrecoverable.

### Project Structure
```
src/
├── domain/           # Pure logic, no framework imports
│   ├── models.py     # Dataclasses, enums, value objects
│   ├── services.py   # Business rules
│   └── protocols.py  # Protocol interfaces
├── infrastructure/   # Adapters (repos, API clients)
├── api/              # Routes, schemas (Pydantic)
└── config.py         # Pydantic settings
tests/
├── test_<behavior>.py
├── conftest.py
└── factories.py
```

## Performance

- `slots=True` on dataclasses — faster access, lower memory.
- `collections.abc` for type hints (`Sequence`, `Mapping`) — accept broader inputs.
- `asyncio.TaskGroup` (3.11+) over `gather` for structured concurrency.
- `tomllib` (3.11+) for config — no third-party toml parser.
- Profile with `py-spy` before optimising.

## Commits

Use `/commit` — one TDD cycle per commit.
