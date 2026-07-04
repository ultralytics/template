# AGENTS.md

This file provides guidance to AI coding agents (Claude Code, etc.) when working with code in this repository. CLAUDE.md is a symlink to this file.

## Core Principles (CRITICAL)

Respecting these principles is critical for every PR.

**Less is more. The simplest solution is the best solution.**

The action hierarchy for every change: **Delete > Replace > Add**. The best code change is a deletion. The second best is modifying what exists. Adding new code is the last resort.

1. **Minimal**: The simplest solution that works. Do not over-engineer, over-abstract, or add code just in case. Three similar lines beat a premature abstraction. Avoid error handling for impossible states, feature flags, compatibility shims, or policy scaffolding unless they are truly required.
2. **Solve at the source**: Do not hack fixes. Solve problems at their root. If something is broken, fix or remove the broken thing. Never patch over a broken abstraction, add workarounds, or add synchronization code for state that should not be duplicated.
3. **Delete ruthlessly**: When replacing code, delete what it replaced. Remove unused imports, functions, types, files, and commented-out code. Git preserves history. Run the repo's relevant dead-code or cleanup check when available.
4. **Replace > Add**: Modify existing code over adding new code. Edit existing files, extend existing components or functions with minimal parameters, and reuse existing utilities. If creating a new file, first prove it cannot fit cleanly in an existing file.
5. **Check existing**: Search the entire repo before creating anything new. If a feature, component, helper, responder, workflow, or utility already solves a similar problem, reuse or adapt it and delete the duplicate path.
6. **Deduplicate**: Do not duplicate existing code when updating the repo. Consolidate or refactor duplicates you find when it is in scope and low risk.
7. **Zero Regression**: Do not break existing features or workflows unless the PR intentionally removes them with evidence.
8. **Production ready**: All changes must be thoroughly debugged, validated, and production ready.

**When fixing bugs, ask: "What can I delete?" before "What can I replace?" before "What should I add?"**

## PR Workflow

After opening a PR:

1. Wait for the automated PR review and auto-format commit from Ultralytics Actions (`format.yml`), then pull and address every finding.
2. Launch an independent adversarial review agent with cold context (just the PR diff and this file) to hunt for bugs, regressions, and Core Principles violations. Fix, push, and repeat with a fresh agent until one reports LGTM.
3. Never fight other commits: Ultralytics Actions pushes auto-format and header commits, and multiple users may work on the same PR. `git pull --rebase` before pushing; never force-push, reset, or revert commits you did not author.
4. After the PR merges, clean up: remove local worktrees and branches for it, then `git checkout main && git pull`.

## Commands

```bash
pip install -e ".[dev]" # install for development

pytest tests -v                                               # run tests (pytest)
pytest tests/test_with_pytest.py::test_add_numbers -v         # run one test
python -m unittest discover tests -v                          # run tests (unittest)
pytest tests -v --cov=./ --cov-report=xml:pytest-coverage.xml # coverage (CI command)
example-cli-command                                           # run the example CLI entry point

ruff format . && ruff check --fix . # format/lint (config in pyproject.toml)
```

CI matrix: Python 3.9/3.13/3.14 × ubuntu/macos/windows. CI runs the tests four ways — pytest and unittest, each with and without coverage — plus the CLI entry point, so tests must pass under both runners.

## Architecture

This is the Ultralytics template for new Python packages — a minimal, fully wired example meant to be copied and adapted. `template/` is the package: `__init__.py` holds `__version__` (read by setuptools dynamic versioning in `pyproject.toml`), and `module1.py` holds the example `add_numbers()`/`main()` backing the `example-cli-command` entry point in `[project.scripts]`. `tests/` demonstrates the same tests in both pytest style (`test_with_pytest.py`) and unittest style (`test_with_unittest.py`). `format.yml` runs Ultralytics Actions on PRs (Ruff, Prettier, codespell, link checks, AI labels/summaries) and commits fixes back to the PR branch.

## Conventions

- License headers (`# Ultralytics 🚀 AGPL-3.0 License - https://ultralytics.com/license`) are added automatically by Ultralytics Actions — don't add or revert them manually.
- Google-style docstrings, `from __future__ import annotations` for modern type hints, line length 120; formatting is auto-applied by `format.yml`.
