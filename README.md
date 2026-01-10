# Clawdbot Ansible Installer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Lint](https://github.com/pasogott/clawdbot-ansible/actions/workflows/lint.yml/badge.svg)](https://github.com/pasogott/clawdbot-ansible/actions/workflows/lint.yml)
[![Ansible](https://img.shields.io/badge/Ansible-2.14+-blue.svg)](https://www.ansible.com/)
[![Multi-OS](https://img.shields.io/badge/OS-Debian%20%7C%20Ubuntu%20%7C%20macOS-orange.svg)](https://www.debian.org/)

Automated, hardened installation of [Clawdbot](https://github.com/clawdbot/clawdbot) with Docker, Homebrew, and Tailscale VPN support for Linux and macOS.

## Features

- 🔒 **Firewall-first**: UFW (Linux) + Application Firewall (macOS) + Docker isolation
- 🔐 **Tailscale VPN**: Secure remote access without exposing services
- 🍺 **Homebrew**: Package manager for both Linux and macOS
- 🐳 **Docker**: Docker CE (Linux) / Docker Desktop (macOS)
- 🛡️ **Multi-OS Support**: Debian, Ubuntu, and macOS
- 🚀 **One-command install**: Complete setup in minutes
- 🔧 **Auto-configuration**: DBus, systemd, environment setup
- 📦 **pnpm installation**: Uses `pnpm install -g clawdbot@latest`

## Quick Start

### Release Mode (Recommended)

Install the latest stable version from npm:

```bash
curl -fsSL https://raw.githubusercontent.com/pasogott/clawdbot-ansible/main/install.sh | bash
```

### Development Mode

Install from source for development or testing:

```bash
# Clone the installer
git clone https://github.com/pasogott/clawdbot-ansible.git
cd clawdbot-ansible

# Install in development mode
ansible-playbook playbook.yml --ask-become-pass -e clawdbot_install_mode=development
```

## What Gets Installed

- Tailscale (mesh VPN)
- UFW firewall (SSH + Tailscale ports only)
- Docker CE + Compose V2 (for sandboxes)
- Node.js 22.x + pnpm
- Clawdbot on host (not containerized)
- Systemd service (auto-start)

## Post-Install

After installation completes, switch to the clawdbot user:

```bash
sudo su - clawdbot
```

Then run the quick-start onboarding wizard:

```bash
clawdbot onboard --install-daemon
```

This will:
- Guide you through the setup wizard
- Configure your messaging provider (WhatsApp/Telegram/Signal)
- Install and start the daemon service

### Alternative Manual Setup

```bash
# Configure manually
clawdbot configure

# Login to provider
clawdbot providers login

# Test gateway
clawdbot gateway

# Install as daemon
clawdbot daemon install
clawdbot daemon start

# Check status
clawdbot status
clawdbot logs
```

## Installation Modes

### Release Mode (Default)
- Installs via `pnpm install -g clawdbot@latest`
- Gets latest stable version from npm registry
- Automatic updates via `pnpm install -g clawdbot@latest`
- **Recommended for production**

### Development Mode
- Clones from `https://github.com/clawdbot/clawdbot.git`
- Builds from source with `pnpm build`
- Symlinks binary to `~/.local/bin/clawdbot`
- Adds helpful aliases:
  - `clawdbot-rebuild` - Rebuild after code changes
  - `clawdbot-dev` - Navigate to repo directory
  - `clawdbot-pull` - Pull, install deps, and rebuild
- **Recommended for development and testing**

Enable with: `-e clawdbot_install_mode=development`

## Security

- **Public ports**: SSH (22), Tailscale (41641/udp) only
- **Docker available**: For Clawdbot sandboxes (isolated execution)
- **Docker isolation**: Containers can't expose ports externally (DOCKER-USER chain)
- **Non-root**: Clawdbot runs as unprivileged user
- **Systemd hardening**: NoNewPrivileges, PrivateTmp

Verify: `nmap -p- YOUR_SERVER_IP` should show only port 22 open.

## Documentation

- [Security Architecture](docs/security.md)
- [Technical Details](docs/architecture.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Agent Guidelines](AGENTS.md)

## Requirements

### Linux (Debian/Ubuntu)
- Debian 11+ or Ubuntu 20.04+
- Root/sudo access
- Internet connection

### macOS
- macOS 11 (Big Sur) or later
- Homebrew will be installed automatically
- Admin/sudo access
- Internet connection

## What Gets Installed

### Common (All OS)
- Homebrew package manager
- Node.js 22.x + pnpm
- Clawdbot via `pnpm install -g clawdbot@latest`
- Essential development tools
- Git, zsh, oh-my-zsh

### Linux-Specific
- Docker CE + Compose V2
- UFW firewall (configured)
- Tailscale VPN
- systemd service

### macOS-Specific
- Docker Desktop (via Homebrew Cask)
- Application Firewall
- Tailscale app

## Manual Installation

### Release Mode (Default)

```bash
# Install dependencies
sudo apt update && sudo apt install -y ansible git

# Clone repository
git clone https://github.com/pasogott/clawdbot-ansible.git
cd clawdbot-ansible

# Install Ansible collections
ansible-galaxy collection install -r requirements.yml

# Run installation
./run-playbook.sh
```

### Development Mode

Build from source for development:

```bash
# Same as above, but with development mode flag
./run-playbook.sh -e clawdbot_install_mode=development

# Or directly:
ansible-playbook playbook.yml --ask-become-pass -e clawdbot_install_mode=development
```

This will:
- Clone clawdbot repo to `~/code/clawdbot`
- Run `pnpm install` and `pnpm build`
- Symlink binary to `~/.local/bin/clawdbot`
- Add development aliases to `.bashrc`

## License

MIT - see [LICENSE](LICENSE)

## Support

- Clawdbot: https://github.com/clawdbot/clawdbot
- This installer: https://github.com/pasogott/clawdbot-ansible/issues
