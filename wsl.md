# WSL: Windows Subsystem for Linux

This document provides a summary and command reference for `wsl`, the Windows Subsystem for Linux — a compatibility layer that allows running Linux distributions directly within Windows.

## Summary

**WSL** (Windows Subsystem for Linux) is a Microsoft technology that enables users to run a Linux environment (such as Ubuntu, Debian, or Fedora) directly on Windows, without using a virtual machine or dual boot.

With WSL, you can use the Linux terminal, package managers, compilers, and developer tools — all seamlessly integrated into Windows.

There are two main versions available:

* **WSL 1** – the original version, with slower file system access but tighter integration with Windows.
* **WSL 2** – the modern version, using a lightweight virtual machine with a full Linux kernel and much better performance.

---

## Most Common WSL Commands

| Command                                 | Description                                                                                     |
| --------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `wsl`                                   | Launches the default Linux distribution in the terminal.                                        |
| `wsl -l` or `wsl --list`                | Lists all installed Linux distributions.                                                        |
| `wsl -l -v`                             | Lists distributions with their WSL version (1 or 2) and current state.                          |
| `wsl --install`                         | Installs WSL along with the default Linux distribution (usually Ubuntu).                        |
| `wsl --install -d <name>`               | Installs a specific Linux distribution, e.g., `wsl --install -d Debian`.                        |
| `wsl --set-version <name> <version>`    | Sets the WSL version (1 or 2) for a specific distribution.                                      |
| `wsl --set-default-version <version>`   | Sets the default WSL version (1 or 2) for new installations.                                    |
| `wsl --set-default <name>`              | Sets the default Linux distribution.                                                            |
| `wsl --terminate <name>`                | Terminates a running distribution.                                                              |
| `wsl --shutdown`                        | Shuts down all running distributions and the WSL 2 virtual machine.                             |
| `wsl -d <name>`                         | Launches a specific distribution, e.g., `wsl -d Ubuntu`.                                        |
| `wsl -e <command>`                      | Runs a specific command inside WSL without opening an interactive shell (e.g. `wsl -e ls -la`). |
| `wsl --export <name> <file.tar>`        | Exports a distribution to a `.tar` file (useful for backups).                                   |
| `wsl --import <name> <path> <file.tar>` | Imports a distribution from a `.tar` archive.                                                   |
| `wsl --unregister <name>`               | Unregisters (removes) a Linux distribution from the system.                                     |
| `wsl --status`                          | Displays information about the kernel version, default distribution, and WSL configuration.     |
| `wsl --update`                          | Updates the WSL Linux kernel to the latest version.                                             |
| `wsl --help`                            | Displays help and all available command options for `wsl`.                                      |

---

## Usage Examples

```bash
# Install Ubuntu in WSL
wsl --install -d Ubuntu

# Check installed distributions and their versions
wsl -l -v

# Change Ubuntu to use WSL 2
wsl --set-version Ubuntu 2

# Run a specific command without entering the shell
wsl -e python3 --version

# Export a distribution to a backup file
wsl --export Ubuntu C:\backup\ubuntu_backup.tar

# Import a distribution from a file
wsl --import UbuntuRestored C:\WSL\ C:\backup\ubuntu_backup.tar
```

---

## Additional Information

* WSL distributions are stored by default in:

  ```
  %LOCALAPPDATA%\Packages\<distribution_name>\LocalState\
  ```

* You can access Linux files from Windows via:

  ```
  \\wsl$\Ubuntu\
  ```

* To install WSL from PowerShell (run as Administrator):

  ```powershell
  wsl --install
  ```

---

