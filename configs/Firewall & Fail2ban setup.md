## Firewall Setup (UFW)

I installed UFW:

```bash
sudo apt install ufw -y
```

Once it finished downloading, I set the firewall to default settings:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Next, I had to allow SSH **before** enabling the firewall, or I would've locked myself out:

```bash
sudo ufw allow ssh
```

I then enabled the firewall:

```bash
sudo ufw enable
```

Finally, I did a status check to confirm everything was working as expected:

```bash
sudo ufw status verbose
```

| Command | Purpose |
|---|---|
| `ufw default deny incoming` | Blocks all inbound traffic by default — nothing gets in unless explicitly allowed |
| `ufw default allow outgoing` | Allows all outbound traffic by default — the box can still reach the internet/updates |
| `ufw allow ssh` | Explicitly permits SSH (port 22) so the firewall doesn't cut off remote access |
| `ufw enable` | Activates the firewall with the rules above |
| `ufw status verbose` | Confirms active rules, default policies, and firewall status |


## Fail2ban Setup

I installed fail2ban:

```bash
sudo apt install fail2ban -y
```

I then created a local config copy instead of editing the original directly, since updates would overwrite it:

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

I opened the local config and made the following changes under the `[sshd]` section:

```ini
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 1h
findtime = 10m
```

I saved the file, then enabled and started the service:

```bash
sudo systemctl enable fail2ban --now
```

I verified it was working with a status check:

```bash
sudo fail2ban-client status sshd
```

| Setting | Purpose |
|---|---|
| `enabled` | Turns the SSH jail on |
| `maxretry` | Number of failed attempts before an IP is banned |
| `bantime` | How long an IP stays banned once triggered |
| `findtime` | The time window in which `maxretry` failures must occur to trigger a ban |
| `logpath` | Which log file fail2ban monitors for failed SSH attempts |
