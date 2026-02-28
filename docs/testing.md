# Testing

---

## Test suite layout

Tests live in `tests/`:

| File | Scope |
|------|-------|
| `fixtures.py` | Shared test fixtures and helpers |
| `helpers.py` | Utility functions for tests |
| `test_proxy.py` | Proxy functionality tests — HTTP methods, auth, PAC, noproxy |
| `test_config.py` | Configuration parsing and validation tests |

---

## Running tests

### Quick run

```bash
make test
```

This runs `pytest` with coverage via `uv run`.

### Manual run

```bash
# Install dev dependencies
uv sync
uv pip install -e .

# Run all tests
uv run python -m pytest tests -q

# Run a specific file
uv run python -m pytest tests/test_proxy.py -q

# Run with coverage
uv run python -m pytest tests --cov --cov-config=pyproject.toml --cov-report=xml

# Run with parallel execution
uv run python -m pytest tests -n 4
```

### With a specific Python version

```bash
uv run -p 3.14 python -m pytest tests -q
```

### Full test matrix via tox

```bash
uv run -p 3.13 tox
```

The `tox` configuration in `pyproject.toml` defines environments for Python
3.10–3.14 and a "binary" environment.

---

## Docker-based testing

The `build.sh` script can execute the test suite inside Docker containers:

```bash
./build.sh -t -i alpine   # Alpine (musl)
./build.sh -t -i ubuntu   # Ubuntu (glibc)
./build.sh -t -i debian   # Debian (glibc)
```

To run the complete test matrix across all supported distributions:

```bash
./build.sh -t
```

---

## Test dependencies

Test dependencies (`pytest`, `pytest-xdist`, `pytest-httpbin`, `pytest-cov`) are
declared in the `dev` dependency group in `pyproject.toml` alongside linting and
type checking tools (`pre-commit`, `ruff`, `mypy`). `uv sync` installs them all.

---

## Coverage

Coverage is configured in `pyproject.toml` under `[tool.coverage.*]`. Branch
coverage is enabled and scoped to the `px` package. Empty files are skipped
in reports.
