# Linux Hardening Cheatsheet

Quick-reference commands used throughout the hardening process. For full context and reasoning behind each step, see [`configs/hardening.md`](../configs/hardening.md).

## System Updates & Package Management

| Command | Purpose |
|---|---|
| `sudo apt update` | Refreshes the local package index |
| `sudo apt upgrade -y` | Installs available upgrades, auto-confirms prompts |
| `sudo dpkg --configure -a` | Finishes configuring packages left half-installed after an interrupted update |
| `sudo apt --fix-broken install` | Resolves broken/missing dependencies |
| `sudo reboot` | Reboots the system to load updated kernel/services cleanly |
| `apt list --installed \| grep <package>` | Confirms exact installed package name/version |

## SSH Key Generation & Auth

| Command | Purpose |
|---|---|
| `ssh-keygen -t ed25519 -C "<comment>"` | Generates an Ed25519 key pair with a label comment |
| `ssh-copy-id <user>@<IP>` | Copies your public key to a remote host's `authorized_keys` |
| `ssh <user>@<IP>` | Connects to a remote host over SSH |

## SSH Server Setup

| Command | Purpose |
|---|---|
| `sudo apt install openssh-server -y` | Installs the SSH daemon |
| `sudo systemctl enable ssh --now` | Enables SSH on boot and starts it immediately |
| `sudo systemctl status ssh` | Checks whether the SSH service is active |
| `sudo systemctl restart sshd` | Restarts SSH to apply config changes |
| `sudo sshd -t` | Tests `sshd_config` syntax before restarting (prevents lockouts) |
| `ss -tlnp \| grep 22` | Confirms port 22 is listening |

## SSH Hardening (`/etc/ssh/sshd_config`)

| Setting | Hardened Value | Purpose |
|---|---|---|
| `PermitRootLogin` | `no` | Disables root login over SSH entirely |
| `PasswordAuthentication` | `no` | Forces key-based login only |
| `PubkeyAuthentication` | `yes` | Enables SSH key authentication |
| `X11Forwarding` | `no` | Disables graphical session forwarding |
| `MaxAuthTries` | `3` | Limits brute-force login attempts per connection |
| `KbdInteractiveAuthentication` | `no` | Closes keyboard-interactive password fallback |

## Firewall (UFW)

| Command | Purpose |
|---|---|
| `sudo apt install ufw -y` | Installs UFW |
| `sudo ufw default deny incoming` | Blocks all inbound traffic by default |
| `sudo ufw default allow outgoing` | Allows all outbound traffic by default |
| `sudo ufw allow ssh` | Permits SSH before enabling the firewall (avoids lockout) |
| `sudo ufw enable` | Activates the firewall |
| `sudo ufw status verbose` | Shows active rules and default policies |

## Fail2ban

| Command | Purpose |
|---|---|
| `sudo apt install fail2ban -y` | Installs fail2ban |
| `sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local` | Creates a local config copy that survives package updates |
| `sudo systemctl enable fail2ban --now` | Enables and starts fail2ban |
| `sudo fail2ban-client status sshd` | Checks ban status/activity for the SSH jail |

| Jail Setting | Purpose |
|---|---|
| `maxretry` | Failed attempts allowed before a ban |
| `bantime` | How long an IP stays banned |
| `findtime` | Time window in which `maxretry` failures trigger a ban |

## AppArmor

| Command | Purpose |
|---|---|
| `sudo aa-status` | Shows AppArmor status and loaded profiles |
| `sudo apt install apparmor-profiles apparmor-profiles-extra -y` | Installs default/extra profile sets |
| `sudo systemctl restart apparmor` | Reloads AppArmor with newly installed profiles |

## Git / GitHub

| Command | Purpose |
|---|---|
| `git remote set-url origin <new-URL>` | Updates local remote after renaming a repo |
| `git remote -v` | Confirms the current remote URL |
