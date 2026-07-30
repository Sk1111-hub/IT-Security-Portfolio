## SSH Hardening

I began by opening the SSH daemon config file:

```bash
sudo nano /etc/ssh/sshd_config
```

Once inside, I changed the following configurations:

```sshconfig
PermitRootLogin prohibit-password → PermitRootLogin no
PasswordAuthentication yes → PasswordAuthentication no
PubkeyAuthentication no → PubkeyAuthentication yes
X11Forwarding yes → X11Forwarding no
MaxAuthTries 6 → MaxAuthTries 3
KbdInteractiveAuthentication yes → KbdInteractiveAuthentication no
```

| Setting | Change | Reason |
|---|---|---|
| `PermitRootLogin` | `prohibit-password` → `no` | Fully disables root login over SSH, even with a key — forces login as a normal user, then `sudo` for privileged actions |
| `PasswordAuthentication` | `yes` → `no` | Disables password-based login entirely, forcing key-based authentication only |
| `PubkeyAuthentication` | `no` → `yes` | Enables SSH key-based login, required since password auth is now disabled |
| `X11Forwarding` | `yes` → `no` | Disables forwarding of graphical X11 sessions over SSH — reduces attack surface, rarely needed on a headless server |
| `MaxAuthTries` | `6` → `3` | Limits brute-force attempts per connection before it's dropped |
| `KbdInteractiveAuthentication` | `yes` → `no` | Disables keyboard-interactive login prompts, closing another password-based fallback path |

## Applying and Verifying SSH Changes

Once the changes were saved, I restarted the SSH service:

```bash
sudo systemctl restart sshd
```

I then opened a second terminal (without closing the first) to confirm I could still SSH into the box:

```bash
ssh sk1111@<VM_IP>
```
