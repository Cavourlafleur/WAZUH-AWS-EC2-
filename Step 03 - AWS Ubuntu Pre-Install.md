# Step 03 - AWS Ubuntu Pre-Install Checks

## Goal

Before installing Wazuh, verify the AWS Ubuntu server is reachable, updated, and has the expected resources.

## Why We Do This Before Installing Wazuh

Before installing Wazuh, we need to confirm:

- What OS is running
- How much RAM the server has
- How many CPU cores are available
- How much disk space exists
- Whether the server is fully updated
- What public IP address it uses

**Simple analogy:** Before building a security office, you inspect the building first.

## Current Server Role

This EC2 instance will become the Wazuh all-in-one server. It will later run:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Filebeat

## Current Instance Choice

```text
Instance type: m7i-flex.large
vCPU: 2
RAM: 8 GiB
Disk: 50 GB gp3 EBS
```

**Important note:** The original target spec was 4 vCPU / 8 GiB RAM / 50 GB disk. The selected lab instance runs 2 vCPU / 8 GiB RAM / 50 GB disk. This is acceptable for a cost-controlled learning lab, but CPU and memory should be monitored during Wazuh installation and use.

---

## Commands Used

### 1. Confirm Current User

```bash
whoami
```

| Part | Meaning |
|---|---|
| `whoami` | Shows the current logged-in user |

**Purpose:** Confirm the server is being accessed as the expected user.

**Expected result:**

```text
ubuntu
```

---

### 2. Check Hostname

```bash
hostname
```

| Part | Meaning |
|---|---|
| `hostname` | Shows the server's computer name |

**Purpose:** Identify the server name assigned by AWS/Ubuntu.

**Example output:**

```text
ip-172-31-xx-xx
```

This naming pattern is normal for AWS EC2 instances.

---

### 3. Check Ubuntu Version

```bash
lsb_release -a
```

| Part | Meaning |
|---|---|
| `lsb_release` | Shows Linux distribution information |
| `-a` | Shows all available OS details |

**Purpose:** Confirm the Ubuntu version before installing Wazuh.

**Expected result (one of):**

```text
Ubuntu 22.04
```

```text
Ubuntu 24.04
```

Wazuh's quickstart guide lists Ubuntu 22.04 and Ubuntu 24.04 as supported operating systems for central components [web:35].

---

### 4. Check CPU Count

```bash
nproc
```

| Part | Meaning |
|---|---|
| `nproc` | Shows the number of CPU processing units available |

**Purpose:** Confirm how many CPU cores/vCPUs the server has.

**Expected result for this instance:**

```text
2
```

---

### 5. Check RAM

```bash
free -h
```

| Part | Meaning |
|---|---|
| `free` | Shows memory/RAM usage |
| `-h` | Human-readable format |

**Purpose:** Confirm available memory before Wazuh installation.

**Expected result:** Around 8 GiB total, showing roughly 7.x GiB usable (Linux reserves a small amount for system use).

---

### 6. Check Disk Space

```bash
df -h
```

| Part | Meaning |
|---|---|
| `df` | Shows filesystem disk space |
| `-h` | Human-readable format |

**Purpose:** Confirm the root disk (`/`) is around 50 GB as expected.

---

### 7. Check Public IP From Inside the Server

```bash
curl -s ifconfig.me
```

| Part | Meaning |
|---|---|
| `curl` | Tool to make web requests/download info |
| `-s` | Silent mode, less noisy output |
| `ifconfig.me` | Website that returns your public IP |

**Purpose:** Confirm the AWS public IPv4 address as seen from the internet, useful for double-checking SSH connectivity details.

---

### 8. Update Ubuntu Package List

```bash
sudo apt update
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator/root |
| `apt` | Ubuntu package manager |
| `update` | Refresh the list of available software updates |

**Purpose:** Refresh Ubuntu's list of available updates. This does not install anything yet — it only checks what updates exist.

---

### 9. Upgrade Installed Packages

```bash
sudo apt upgrade -y
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator/root |
| `apt` | Ubuntu package manager |
| `upgrade` | Install available updates for installed packages |
| `-y` | Automatically answer yes to prompts |

**Purpose:** Install available Ubuntu updates before installing Wazuh. This may take a few minutes.

---

### 10. Check if Reboot Is Required

```bash
test -f /var/run/reboot-required && echo "Reboot required" || echo "No reboot required"
```

| Part | Meaning |
|---|---|
| `test -f /var/run/reboot-required` | Checks if the reboot-required file exists |
| `&&` | Run the next command only if the check is true |
| `echo "Reboot required"` | Prints that a reboot is needed |
| `\|\|` | Run the next command only if the check is false |
| `echo "No reboot required"` | Prints that no reboot is needed |

**Purpose:** Determine whether Ubuntu needs a restart after installing updates.

---

### 11. Reboot if Needed

```bash
sudo reboot
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator/root |
| `reboot` | Restart the server |

**Purpose:** Restart the server if updates require it.

**Reconnect after reboot** (wait about 1 minute first):

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@<EC2_PUBLIC_IP>
```

---

### 12. Check System Health After Update

```bash
uptime
```

| Part | Meaning |
|---|---|
| `uptime` | Shows how long the server has been running and current system load |

**Purpose:** Confirm the server is alive and check how busy it is after reboot.

Re-run these two to confirm resources still look correct:

```bash
df -h
```

```bash
free -h
```

---

## Important: Do Not Install Wazuh Yet

Stop after completing these checks. Do **not** run this command yet:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

The Wazuh quickstart guide uses this installation assistant to install the Wazuh central components — the Wazuh server, indexer, and dashboard — all on the same host [web:35]. This will be run in the next step, slowly, with every part explained.

---

## Security Notes

SSH should remain restricted in the AWS Security Group.

**Recommended:**

```text
22/TCP from My IP only
```

**Avoid:**

```text
22/TCP from Anywhere / 0.0.0.0/0
```

## Cost Notes

This lab uses AWS credits. Cost-control reminders:

- Stop the EC2 instance when not actively studying
- Keep disk at 50 GB for now
- Avoid unused Elastic IPs
- Create an AWS Budget alert

## Status Checklist

- [ ] SSH access confirmed
- [ ] Ubuntu version checked
- [ ] CPU checked
- [ ] RAM checked
- [ ] Disk checked
- [ ] Package list updated
- [ ] Packages upgraded
- [ ] Reboot completed if required

## Next Step

Install Wazuh all-in-one central components using the official Wazuh installation assistant [web:38].
