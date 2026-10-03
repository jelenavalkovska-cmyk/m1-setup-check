# Explain what make test does

`make test` runs the `test` target in the `Makefile`, which is a single command:

```make
test:
	pytest -q
```

It runs pytest from the repo root in quiet mode (`-q`), so you get dots and a short summary. The repo has no pytest config file (`pytest.ini`, `pyproject.toml`, `setup.cfg` or `conftest.py`), so pytest uses its default discovery: any `test_*.py` or `*_test.py` file under the current directory.

Right now that finds one file, `tests/test_smoke.py`, which has two tests:

1. **`test_openapi_document_can_be_loaded`**: parses `docs/openapi.yaml` with PyYAML. It checks that the `openapi` field starts with `3.` and that `paths` is not empty. It only confirms the contract is valid YAML with a basic OpenAPI 3 shape. It doesn't check the contract in detail; that's what `make lint-contract` does, by running `tools/lint_contract.py`.
2. **`test_participant_files_are_present`**: checks that these files exist: `.claude/settings.json`, `.devcontainer/devcontainer.json`, `CLAUDE.md`, `Makefile`, `tracker/CR-2.md` and `tracker/README.md`. If any are missing, it fails and lists them in Latvian ("Trūkst faili: …").

How it relates to the other targets:

- `make verify-setup` also runs these smoke tests (`pytest -q tests/test_smoke.py`), but first it checks the Python version, the required packages (including Pydantic v2), the Claude CLI, and that `setup/claude-answer.md` exists.
- `make test` will automatically pick up any new test files you add later, so it grows with the project. Per `CLAUDE.md`, run it after every change, along with `make lint-contract` whenever you change `docs/openapi.yaml`.
