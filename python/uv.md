# UV: The All-in-One Python Project and Package Manager

This document provides a summary and command reference for `uv`, a high-performance Python package and project manager featured.

## Summary

`uv` is an exceptionally fast, all-in-one tool designed to streamline Python development workflows. Written in Rust by the creators of `ruff`, it aims to replace multiple tools like `pip`, `pip-tools`, `virtualenv`, and `poetry` with a single, cohesive interface.

The key advantages of `uv` its incredible speed and comprehensive feature set. It can create virtual environments, install packages, manage dependencies, and run scripts, all from a single command-line tool. The `uv` is significantly faster at installing packages compared to traditional methods, partly due to its efficient dependency resolution and use of a global cache.

## `uv` Commands

Here are the `uv` most usefull commands, along short description.

| Command | Description |
| --- | --- |
| `uv pip install <package>` | Installs one or more packages into the current virtual environment. Much faster than `pip`. |
| `uv pip uninstall <package>` | Removes one or more packages from the current environment. |
| `uv venv` | Creates a new virtual environment in the `.venv` directory. |
| `uv init` | Initializes a new Python project, creating a `pyproject.toml` file. |
| `uv add <package>` | Adds a package as a project dependency in `pyproject.toml` and installs it. |
| `uv remove <package>` | Removes a package from `pyproject.toml` and uninstalls it from the environment. |
| `uv run <script>` | Runs a command or script within the project's virtual environment. |
| `uv sync` | Installs the dependencies specified in `pyproject.toml` and `uv.lock` into the environment. |
| `uv lock` | Resolves dependencies from `pyproject.toml` and creates a `uv.lock` file. |
| `uv python list` | Lists all available Python interpreters that `uv` can find. |
| `uv python pin <version>` | Sets a specific Python version for the current project. |
| `uvx <command>` | A convenient shortcut for `uv tool run <command>`, which executes a tool in a temporary environment. |
| `uv add <package> --dev` | Adds a package as a development-only dependency, keeping your production environment clean. |
| `uv pip install -r requirements.txt` | A drop-in replacement for `pip` that allows you to install packages from a `requirements.txt` file at much higher speeds. |
| `uv pip compile <file>` | Compiles a `requirements.in` file into a fully pinned `requirements.txt` file, similar to `pip-compile`. |
| `uv cache clean` | Clears `uv`'s global cache to free up disk space. |
