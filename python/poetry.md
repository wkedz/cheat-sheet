# Poetry: Python Dependency Management and Packaging

This document provides a summary and command reference for `poetry`, a modern tool for managing Python project dependencies and packaging.

## Summary

`Poetry` is a tool designed to simplify dependency management and packaging in Python. It helps you declare, manage, and install dependencies for your projects, ensuring you have a reliable and consistent environment everywhere.

The key advantage of `Poetry` is its use of a single `pyproject.toml` file to manage all project metadata, dependencies, scripts, and configurations. It generates a `poetry.lock` file to ensure deterministic builds, meaning everyone on your team uses the exact same versions of dependencies. It also automatically creates and manages a dedicated virtual environment for your project, isolating it from other Python projects on your system.

## `poetry` Commands

Here are the most useful `poetry` commands for daily development, following the 80/20 rule.

| Command | Description |
| --- | --- |
| `poetry --version` | Shows the currently installed version of Poetry. |
| `poetry add <package> --group dev` | Adds a development-only dependency (e.g., pytest, ruff, black). |
| `poetry add <package>` | Adds a production dependency to `pyproject.toml` and installs it. |
| `poetry build` | Builds the source and wheel distributions for your project into the `dist/` folder. |
| `poetry check` | Validates the `pyproject.toml` file for errors or inconsistencies. |
| `poetry export -f requirements.txt` | Exports the project's dependencies to a `requirements.txt` file for interoperability. |
| `poetry init` | Interactively creates a `pyproject.toml` in an existing directory. |
| `poetry install` | Installs all dependencies from `poetry.lock` (if present) or `pyproject.toml`. |
| `poetry lock` | Resolves and locks dependencies from `pyproject.toml` without installing them. |
| `poetry new <project-name>` | Creates a new project with a standard directory structure and `pyproject.toml`. |
| `poetry publish` | Publishes the package to a repository like PyPI. Requires configuration. |
| `poetry remove <package>` | Removes a dependency from `pyproject.toml` and uninstalls it. |
| `poetry run <command>` | Executes a command inside the project's virtual environment. E.g., `poetry run python my_script.py`. |
| `poetry shell` | Activates the project's virtual environment, spawning a new shell. Type `exit` to leave. |
| `poetry show --tree` | Displays all installed packages and their dependencies in a tree structure. Great for debugging. |
| `poetry update <package>` | Updates only a specific package to its latest allowed version. |
| `poetry update` | Updates all dependencies to the latest versions allowed by `pyproject.toml` and updates the lock file. |