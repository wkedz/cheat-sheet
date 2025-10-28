Of course! Here is a summary cheat sheet for using `uv` workspaces in Markdown format.

# `uv` Workspace Cheat Sheet

`uv` workspaces help you manage multiple Python projects (packages) within a single repository (a monorepo). This approach centralizes dependency management, prevents code duplication, and simplifies your development workflow.

## Core Concepts

A `uv` workspace consists of a **root** `pyproject.toml` that defines the workspace and several **member** packages, each with its own `pyproject.toml`. All packages share a single virtual environment created at the root level.

### 1. Project Structure

A typical monorepo using `uv` workspaces looks like this:

```plaintext
my-monorepo/
├── .venv/                 # The single, shared virtual environment
├── packages/              # Directory containing all your projects
│   ├── api/               # First project (e.g., a FastAPI app)
│   │   ├── pyproject.toml # Dependencies specific to the API
│   │   └── ...            # Source code for the API
│   │
│   ├── cli/               # Second project (e.g., a CLI tool)
│   │   ├── pyproject.toml # Dependencies specific to the CLI
│   │   └── ...            # Source code for the CLI
│   │
│   └── core/              # A shared, internal library
│       ├── pyproject.toml # Defines the shared package
│       └── src/core/...   # Source code for the shared library
│
├── pyproject.toml         # The ROOT workspace configuration
└── uv.lock                # The single lock file for the entire workspace
```

### 2. Root `pyproject.toml` Configuration

This file is the control center for your workspace. It defines which directories are members and can specify shared dependencies.

```toml
# ./pyproject.toml

[project]
name = "my-workspace"
dependencies = [
    # Dependencies needed by MULTIPLE packages can go here
    "python-dotenv",
    "core",  # This refers to our local 'core' package
]

# --- Workspace Configuration ---

# [tool.uv.workspace] defines the members of the workspace.
# `uv` will look for a `pyproject.toml` in each of these directories.
[tool.uv.workspace]
members = ["packages/*"]

# --- Local Package Sources ---

# [tool.uv.sources] tells uv where to find local packages.
# This makes `core = { workspace = true }` work.
[tool.uv.sources]
core = { workspace = true }
# You can also point to a specific path if needed:
# core = { path = "packages/core", editable = true }
```

### 3. Package `pyproject.toml` Configuration

Each individual package defines its own specific dependencies.

```toml
# ./packages/api/pyproject.toml

[project]
name = "api-app"
description = "My FastAPI application"
dependencies = [
    "fastapi",
    "uvicorn",
]
```

## Essential `uv` Workspace Commands

Run these commands from the **root directory** of your monorepo.

| Command                     | Description                                                                                             |
| --------------------------- | ------------------------------------------------------------------------------------------------------- |
| `uv venv`                   | Creates the shared virtual environment (e.g., in `.venv`).                                              |
| `uv sync --all-packages`    | Installs/updates all dependencies from the root and all member packages into the shared virtual environment. This is your most-used command. |
| `uv run <package_name> ...` | Runs a command within the context of a specific package. `uv` finds the package based on the `[project].name` in its `pyproject.toml`.  *This command is not yet fully implemented in `uv` as of late 2024 but is part of the roadmap.* |
| `uv run --package <name> ...` | (Alternative/Future Syntax) Explicitly specify a package to run a command in. |
| `uv pip install <dep>`      | Adds a dependency to the root `pyproject.toml` and installs it.                                         |
| `uv pip install -p <pkg> <dep>` | Adds a dependency to a *specific package's* `pyproject.toml` and installs it.                           |
| `uv pip uninstall <dep>`    | Removes a dependency from the root `pyproject.toml` and the environment.                                |

## Example Workflow

1.  **Set Up Virtual Environment:**
    ```bash
    # At the root of your project
    uv venv
    source .venv/bin/activate
    ```

2.  **Define Workspace:**
    *   Create your root `pyproject.toml` and add the `[tool.uv.workspace]` section.
    *   Create your individual package directories (e.g., `packages/api`, `packages/cli`).

3.  **Add Dependencies:**
    *   **Shared Dependency:** To add `httpx` to all projects, add it to the `[project].dependencies` in the root `pyproject.toml`.
    *   **Specific Dependency:** To add `fastapi` only to your `api` project, add it to `packages/api/pyproject.toml`.

4.  **Install Everything:**
    ```bash
    # This reads all pyproject.toml files and installs everything needed
    uv sync --all-packages
    ```

5.  **Run an Application:**
    Since `uv run <package>` is a future feature, the current way to run a script is to specify its path directly. `uv` will use the activated workspace environment.
    ```bash
    # Assuming 'main.py' starts your CLI app
    uv run python packages/cli/main.py

    # Assuming 'main.py' starts your API server
    uv run python packages/api/main.py
    ```

---