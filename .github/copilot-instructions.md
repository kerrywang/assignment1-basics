<!-- Copilot / AI agent instructions for contributors working on CS336 assignment1-basics -->
# Copilot instructions (short & actionable)

This file contains repository-specific guidance to help an automated coding assistant be productive immediately.

Core facts
- Language: Python 3.11+ (see `pyproject.toml`).
- Virtual environment / dependency manager: `uv` is used to solve and activate environments. Prefer `uv run <cmd>` when running project commands.
- Tests: pytest-based test suite lives in `tests/`. Run with `uv run pytest` (README) or `uv run pytest -q` for quieter output.

Quick start commands (use `uv` when possible)
- Run unit tests: `uv run pytest`
- Run a single test file: `uv run pytest tests/test_tokenizer.py::test_name`
- Run an interactive file with the project environment: `uv run python cs336_basics/pretokenization_example.py`

What to read first (high-value files)
- `README.md` — setup, `uv` usage, and data download steps.
- `pyproject.toml` — dependency pins (note special torch pins for macOS Intel), pytest config, and ruff settings.
- `tests/conftest.py` — custom fixtures, snapshot helpers (`Snapshot`, `NumpySnapshot`), and test utilities. Many tests rely on these fixtures.
- `cs336_basics/pretokenization_example.py` — demonstrates byte-level tokenization helpers and chunking pattern used by assignment utilities.
- `tests/adapters.py` — the primary place you're expected to implement functions to connect your code to the tests (initial tests intentionally raise NotImplementedError).

Project-specific conventions and patterns (examples)
- Environment isolation: Always run tests and examples via `uv run ...` so dependencies and toolchain are automatically solved.
- Snapshot testing: Tests use custom snapshot helpers and snapshot files under `tests/_snapshots`. See `tests/conftest.py` for the `Snapshot` and `NumpySnapshot` APIs. Snapshots are stored as `.pkl` or `.npz` files. Only update these intentionally — inspect test failures to know what changed.
- Adapters wiring: `tests/adapters.py` is the integration surface that tests import to call your implementation. Early tests will fail with `NotImplementedError` until you implement the required functions here.
- Byte-level token handling: The repo handles tokens at the bytes level in some utilities (see `pretokenization_example.py`). Use `.encode()`/`.decode()` carefully in tests relying on exact byte offsets.

Testing and debugging notes
- Tests are run with pytest using options from `pyproject.toml` (see `[tool.pytest.ini_options]` — `-s` is enabled). Running under `uv` ensures the correct Python and deps.
- Snapshot mismatch diagnosis: Read the `assert` messages in failing tests. Snapshots are loaded from `tests/_snapshots/<test_name>.(pkl|npz)`; update only after verifying outputs are correct.
- Reproducibility: Many fixtures seed torch/numpy to make tests deterministic (see `conftest.py`). If tests are flaky, check whether seeds were modified.

Tips for code changes an AI agent might make
- Small, focused edits are preferred. Many tests check numerical precision — prefer using `numpy.testing.assert_allclose` with tolerances where appropriate.
- When adding helpers that touch tensors, prefer keeping CPU tensors in tests (conftest loads model state with `map_location='cpu'`). Avoid GPU-only code.
- Follow style settings in `pyproject.toml` for `ruff` (line-length 120). Keep public API surfaces unchanged unless a test requires it.

Common pitfalls to avoid
- Running `pytest` without `uv`: dependencies (torch pins, etc.) may not be resolved correctly on contributor machines.
- Updating snapshots blindly: always inspect failing outputs first.
- Assuming GPU availability: tests run on CPU in CI and local development.

Where to look for more context
- `tests/` — tests illustrate expected behavior and edge-cases. Use them as executable documentation.
- `tests/_snapshots/` — reference tensors/arrays used to validate implementations.

If something is unclear
- Ask the repo owner for the expected behavior for a failing test, or open an issue with a failing test snapshot and a small reproducible snippet.

Request for feedback
- If any section is unclear or you'd like more examples (for e.g., a short walkthrough of a failing tokenizer test), tell me which area and I'll expand.

---
Small, focused instructions — follow the repo tests and `conftest.py` as the canonical source of truth.
