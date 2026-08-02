# AGENTS.md

This file provides guidance to AI coding agents (Claude Code, etc.) when working with code in this repository. CLAUDE.md is a symlink to this file.

## Core Principles (CRITICAL)

**Less is more. The simplest solution is the best solution.** The action hierarchy for every change: **Delete > Replace > Add**.

1. **Solve at the owner**: Put behavior in the code path that owns or observes it. For fixes, never guard a symptom with a staleness check, initialization flag, skip-first-call branch, or `try/except` around broken logic; relocate the trigger and delete the wrong path. For features, extend the existing owner rather than creating a parallel abstraction.
2. **Search and reuse first**: Search the whole repository before creating a feature, component, helper, workflow, or utility. Reuse or adapt what exists, consolidate in-scope duplication in the shared owner, and delete duplicate paths. Three similar lines beat a helper nobody else calls.
3. **Delete and modify existing code before creating new code**: Bugfixes are net-negative by default unless deletion and relocation are demonstrably impossible. A new file must first prove it cannot fit cleanly in an existing owner.
4. **Keep scope minimal**: Implement only the simplest complete solution. Avoid impossible-state handling, speculative flags, compatibility shims, policy scaffolding, and unrelated cleanup. Tests are out of scope by default — rely on existing coverage and focused validation; only an uncovered, high-risk regression path justifies minimal new test code.
5. **Ship zero-regression, production-ready changes**: Understand what you remove instead of retaining broken code as insurance. Remove unused imports, functions, types, files, and comments; run relevant cleanup checks; and thoroughly debug and validate the changed owner. Do not break existing features or workflows unless the PR intentionally removes them with evidence.

**Review gate:** for every addition, the reviewer decides whether deleting or changing existing code would have fixed the problem instead — if it would, that is a blocking finding. A missing or thin PR description is never itself a finding.

NEVER push to `main`. NEVER force push. Always start work in a new git worktree (`git worktree add`) on a feature branch and open a PR — never edit the primary checkout directly, it may hold in-flight work.

## PR Workflow

After opening a PR:

1. Wait for the automated PR review and auto-format commit from Ultralytics Actions (`format.yml`), then pull and address every finding.
2. Review the full diff in-session against the Core Principles, performance, and the review gate above, then batch the fixes into one commit and push. After each round of bot or human commits, pull and resume the same reviewer on `<last-reviewed-sha>..HEAD` plus anything that delta could have invalidated. Repeat until the local head matches the live head.
3. Hand off or merge only on a clean final pass: one cold full-diff review returning LGTM with no findings, on a head that is still live at merge time.
4. Never fight other commits: Ultralytics Actions pushes auto-format and header commits, and multiple users may work on the same PR. `git pull --rebase` before pushing; never reset or revert commits you did not author.
5. After the PR merges, clean up: remove local worktrees and branches for it, then `git checkout main && git pull`.

## Commands

```bash
uv pip install -e ".[dev]" # install for development

pytest tests -v                                               # run tests (pytest)
pytest tests/test_with_pytest.py::test_add_numbers -v         # run one test
python -m unittest discover tests -v                          # run tests (unittest)
pytest tests -v --cov=./ --cov-report=xml:pytest-coverage.xml # coverage (CI command)
example-cli-command                                           # run the example CLI entry point

ruff format . && ruff check --fix . # format/lint (uv pip install ruff; config in pyproject.toml)
```

CI matrix: Python 3.9/3.13/3.14 × ubuntu/macos/windows. CI runs the tests four ways — pytest and unittest, each with and without coverage — plus the CLI entry point, so tests must pass under both runners.

## Architecture

This is the Ultralytics template for new Python packages — a minimal, fully wired example meant to be copied and adapted. `template/` is the package: `__init__.py` holds `__version__` (read by setuptools dynamic versioning in `pyproject.toml`), and `module1.py` holds the example `add_numbers()`/`main()` backing the `example-cli-command` entry point in `[project.scripts]`. `tests/` demonstrates the same tests in both pytest style (`test_with_pytest.py`) and unittest style (`test_with_unittest.py`). `format.yml` runs Ultralytics Actions on PRs (Ruff, Prettier, codespell, link checks, AI labels/summaries) and commits fixes back to the PR branch. `publish.yml` tags, releases, and (optionally) publishes to PyPI when `__version__` is bumped on `main`; its `check` job intentionally omits the `github.actor` maintainer gate that product repos keep for security, because a fork supplies its own.

## Conventions

- License headers (`# Ultralytics 🚀 AGPL-3.0 License - https://ultralytics.com/license`) are added automatically by Ultralytics Actions — don't add or revert them manually.
- Google-style docstrings, `from __future__ import annotations` for modern type hints, line length 120; formatting is auto-applied by `format.yml`.
