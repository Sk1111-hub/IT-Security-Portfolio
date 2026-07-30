## Enabling AppArmor

I first checked whether AppArmor was already running:

```bash
sudo aa-status
```

This returned an error — AppArmor was loaded and enforcing, but no profiles were installed, so there was nothing for it to actually apply.

To fix this, I installed the default and extra profile packages:

```bash
sudo apt install apparmor-profiles apparmor-profiles-extra -y
```

I then restarted the AppArmor service to load the newly installed profiles:

```bash
sudo systemctl restart apparmor
```

Running the status check again confirmed the fix — the profiles that were previously missing now showed up under `aa-status`, with each one loaded in either enforce or complain mode.

```bash
sudo aa-status
```
