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
