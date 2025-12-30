# dotfiles
chez moi dotfiles

# Chezmoi Bootstrap Guide (Debian 12 / Bookworm)

This document describes the **minimum required steps** to bootstrap a fresh
Debian-based system using **chezmoi**, including Rust, shell setup, and tools.

The goal is:
- per-user installation
- reproducible setup
- no global dotfile pollution
- one command to apply everything

---

## Requirements

You need:
- Debian 12 (Bookworm) or compatible
- a non-root user with sudo access
- outbound HTTPS access (GitHub, crates.io)

---

## Step 1: Install base prerequisites

```bash
sudo apt update
sudo apt install -y curl git sudo
```

---

## Step 2: Install chezmoi (user-local)

Chezmoi is installed **only for the current user**, not system-wide.

```bash
mkdir -p ~/.local/bin
curl -fsLS get.chezmoi.io/lb | sh
```

Make it available for the current shell session:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Verify installation:

```bash
chezmoi --version
```

---

## Step 3: One-command bootstrap (init + apply)

This single command will:

- clone the dotfiles repository
- install base system packages
- install Rust (rustup)
- install pinned tools (helix, zellij, starship, typst, …)
- configure zsh
- set zsh as the login shell (per user)

```bash
chezmoi init --apply https://github.com/NBKvega/dotfiles
```

That’s it.

---

## What happens automatically

Chezmoi executes the following **once**, in a guaranteed order:

1. `run_once_10_install_base.sh`
2. `run_once_20_install_rust.sh`
3. `run_once_30_install_tools.sh`
4. `run_once_90_set_shell.sh`

---

## First login after installation

After the bootstrap finishes:

```bash
exit
```

Log in again to activate:
- zsh as login shell
- updated PATH
- starship prompt
- zoxide
- plugins

---

## Common pitfalls & notes

### `.local/bin` permissions

Chezmoi installs binaries into:

```text
~/.local/bin
```

This directory must be **owned and writable by the user**.

---

### Rust PATH warning (expected)

Rust prints:

> “Cargo’s bin directory is not in your PATH”

This is **intentional**.

PATH handling is done via:
- `~/.zprofile`

---

### Ghostty / TERM warnings

If `TERM=xterm-ghostty` is unsupported on a remote system,
the configuration automatically falls back to:

```text
xterm-256color
```

---

## Re-running later

```bash
chezmoi update
```

Preview only:

```bash
chezmoi apply --dry-run -v
```

---

## Done ✅
