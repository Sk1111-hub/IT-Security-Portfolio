# 07/28/2026 

## Overview
Notes on hardening steps taken to secure this VM/box, including rationale for each change.

## 1. System Updates
```bash
sudo apt update && sudo apt upgrade -y
```
Keeps packages current so newer tools/config changes aren't fighting outdated dependencies.

## 2. Recovering from Interrupted Update

After running the update command, I ran into an issue: my laptop briefly lost internet connection while the update/upgrade was in progress, which left some packages partially configured.

To reconfigure the unfinished packages and fix this, I ran:

```bash
sudo dpkg --configure -a
```
`dpkg --configure -a` basically finishes configuring any packages that were left in a half-installed state by re-running the post-install setup steps for anything that got interrupted, without re-downloading anything. 
It the standard fix for any time apt gets cut off mid-process.

## 3. Finishing the Fix and Verifying

After reconfiguring the unfinished packages, I ran:

```bash
sudo apt --fix-broken install
sudo apt update && sudo apt upgrade -y
```

Then rebooted to make sure everything loaded cleanly:

```bash
sudo reboot
```

After reboot, everything was up and running with no errors.

## 4. SSH Key Generation

Since I didn't already have a key pair on my local machine, I generated one:

```bash
ssh-keygen -t ed25519 -C "vm key"
```

I entered a passphrase to encrypt the private key.

## 5. Network Adapter Issue

I then ran into an issue because my network adapter still had default settings (NAT), which was preventing the connection I needed. I powered off the VM and switched the network configuration from **NAT** to **Bridge Adapter**.

## 6. Discovering SSH Wasn't Running

After the network fix, I discovered SSH wasn't running on the VM at all.

## 7. Installing and Enabling OpenSSH Server

```bash
sudo apt install openssh-server -y
```

Enabled the service to start now and on every future boot:

```bash
sudo systemctl enable ssh --now
```

Confirmed it was running:

```bash
sudo systemctl status ssh
```

Checked that port 22 was actually listening:

```bash
ss -tlnp | grep 22
```
