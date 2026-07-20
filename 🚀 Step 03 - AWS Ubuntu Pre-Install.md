# 🚀 Step 03 - AWS Ubuntu Pre-Install Checks

## 🎯 Goal

Before installing Wazuh, verify that the AWS Ubuntu server is reachable, updated, and has the expected resources.

## 🧠 Why This Step Matters

Before installing Wazuh, it is smart to confirm:

- What OS is running 🐧
- How much RAM the server has 💾
- How many CPU cores are available 🧮
- How much disk space exists 🗂️
- Whether the server is fully updated 🔄
- What public IP address it uses 🌍

### 🧒 Simple explanation

Before building a security office, inspect the building first 🏢🔍

---

## 🖥️ Current Server Role

This EC2 instance will become the Wazuh all-in-one server. Later, it will run:

- Wazuh Manager 🛡️
- Wazuh Indexer 📚
- Wazuh Dashboard 📊
- Filebeat 📦

---

## ⚙️ Current Instance Choice

```text
Instance type: m7i-flex.large
vCPU: 2
RAM: 8 GiB
Disk: 50 GB gp3 EBS
```

> ⚠️ Important note: The original target was 4 vCPU / 8 GiB RAM / 50 GB disk.  
> This lab instance uses 2 vCPU / 8 GiB RAM / 50 GB disk.  
> That is okay for a cost-controlled learning lab, but CPU and memory should be watched during installation and testing.

---

## 🛠️ Commands Used

## 1️⃣ Confirm Current User

```bash
whoami
```

| Part | Meaning |
|---|---|
| `whoami` | Shows the current logged-in user |

### ✅ Purpose

Confirm that the session is running as the expected Ubuntu user.

### 👶 Kid meaning

Tell me who I am logged in as.

### 📌 Expected output

```text
ubuntu
```

---

## 2️⃣ Check Hostname

```bash
hostname
```

| Part | Meaning |
|---|---|
| `hostname` | Shows the server's computer name |

### ✅ Purpose

Identify the server name assigned by AWS and Ubuntu.

### 👶 Kid meaning

Tell me this server's name.

### 📌 Example output

```text
ip-172-31-xx-xx
```

That naming style is normal in AWS ☁️

---

## 3️⃣ Check Ubuntu Version

```bash
lsb_release -a
```

| Part | Meaning |
|---|---|
| `lsb_release` | Shows Linux distribution information |
| `-a` | Shows all available OS details |

### ✅ Purpose

Confirm the Ubuntu version before installing Wazuh.

### 👶 Kid meaning

Tell me what version of Ubuntu this server is using.

### 📌 Expected output

```text
Ubuntu 22.04
```

or

```text
Ubuntu 24.04
```

Wazuh quickstart lists Ubuntu 22.04 and Ubuntu 24.04 as supported operating systems for central components [web:44].

---

## 4️⃣ Check CPU Count

```bash
nproc
```

| Part | Meaning |
|---|---|
| `nproc` | Shows the number of CPU processing units available |

### ✅ Purpose

Confirm how many CPU cores or vCPUs the server has.

### 👶 Kid meaning

Tell me how many CPU brains this server has.

### 📌 Expected output

```text
2
```

That means this EC2 instance has **2 vCPU** 🧠🧠

---

## 5️⃣ Check RAM

```bash
free -h
```

| Part | Meaning |
|---|---|
| `free` | Shows memory/RAM usage |
| `-h` | Human-readable format |

### ✅ Purpose

Confirm available memory before installing Wazuh.

### 👶 Kid meaning

Show me how much memory the server has in an easy format.

### 📌 Expected result

You should see about **8 GiB RAM**, usually shown as around **7.x GiB usable** because Linux reserves a little memory for system use 💾

---

## 6️⃣ Check Disk Space

```bash
df -h
```

| Part | Meaning |
|---|---|
| `df` | Shows filesystem disk space |
| `-h` | Human-readable format |

### ✅ Purpose

Confirm the root disk is around 50 GB.

### 👶 Kid meaning

Show me how much hard drive space this server has.

### 📌 What to look for

Look for the `/` root filesystem. It should be around **50G** 🗂️

---

## 7️⃣ Check Public IP From Inside the Server

```bash
curl -s ifconfig.me
```

| Part | Meaning |
|---|---|
| `curl` | Tool to make web requests |
| `-s` | Silent mode |
| `ifconfig.me` | Website that returns your public IP |

### ✅ Purpose

Confirm the public IPv4 address that the server shows to the internet.

### 👶 Kid meaning

Ask the internet: what public IP does this AWS server appear to use?

---

## 8️⃣ Update Ubuntu Package List

```bash
sudo apt update
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator/root |
| `apt` | Ubuntu package manager |
| `update` | Refresh the list of available package updates |

### ✅ Purpose

Refresh Ubuntu's list of available updates before installation.

### 👶 Kid meaning

Ask Ubuntu to check what software updates exist.

### ⚠️ Important

This does **not** install updates yet. It only refreshes the update list 🔄

---

## 9️⃣ Upgrade Installed Packages

```bash
sudo apt upgrade -y
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator/root |
| `apt` | Ubuntu package manager |
| `upgrade` | Install available updates for installed packages |
| `-y` | Automatically answer yes |

### ✅ Purpose

Install the available Ubuntu updates before installing Wazuh.

### 👶 Kid meaning

Install the available updates without asking me yes/no over and over.

### ⏳ Note

This may take a few minutes.

---

## 🔟 Check if Reboot Is Required

```bash
test -f /var/run/reboot-required && echo "Reboot required" || echo "No reboot required"
```

| Part | Meaning |
|---|---|
| `test -f /var/run/reboot-required` | Check if the reboot-required file exists |
| `&&` | If true, run the next command |
| `echo "Reboot required"` | Print reboot needed |
| `\|\|` | If false, run the next command |
| `echo "No reboot required"` | Print reboot not needed |

### ✅ Purpose

Check whether Ubuntu needs a restart after updates.

### 👶 Kid meaning

Check if Ubuntu needs to restart after updating.

---

## 1️⃣1️⃣ Reboot if Needed

```bash
sudo reboot
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator/root |
| `reboot` | Restart the server |

### ✅ Purpose

Restart the server if updates require it.

### 👶 Kid meaning

Restart the AWS Ubuntu server.

### 🔁 Reconnect after reboot

Wait about 1 minute, then SSH back in:

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@<EC2_PUBLIC_IP>
```

---

## 1️⃣2️⃣ Check System Health After Update

```bash
uptime
```

| Part | Meaning |
|---|---|
| `uptime` | Shows how long the server has been running and current system load |

### ✅ Purpose

Confirm the server is alive and check how busy it is after the update/reboot.

### 👶 Kid meaning

Tell me if the server is alive and how busy it is.

### 🔍 Then run again

```bash
df -h
```

```bash
free -h
```

These confirm that disk and RAM still look healthy after the update ✅

---

## ⛔ Important: Do Not Install Wazuh Yet

Stop after completing these checks.

Do **not** run this yet:

```bash
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

Wazuh quickstart uses the installation assistant to install the Wazuh central components on the same host, including the Wazuh server, indexer, and dashboard [web:44].

That comes in the **next step** 😎

---

## 🔐 Security Notes

SSH should stay restricted in the AWS Security Group.

### ✅ Recommended

```text
22/TCP from My IP only
```

### ❌ Avoid

```text
22/TCP from Anywhere / 0.0.0.0/0
```

AWS security groups act like a virtual firewall for EC2 instances, and inbound rules control incoming traffic to the instance [web:51].

---

## 💸 Cost Notes

This lab uses AWS credits, so keep costs under control:

- Stop the EC2 instance when not studying 🛑
- Keep disk at 50 GB for now 💾
- Avoid unused Elastic IPs 🌐
- Create an AWS Budget alert 💰

---

## ✅ Status Checklist

- [ ] SSH access confirmed
- [ ] Ubuntu version checked
- [ ] CPU checked
- [ ] RAM checked
- [ ] Disk checked
- [ ] Public IP checked
- [ ] Package list updated
- [ ] Packages upgraded
- [ ] Reboot completed if required

---

## ⏭️ Next Step

Install Wazuh all-in-one central components using the official Wazuh installation assistant [web:44].

---

## 🗣️ Interview Explanation

This step shows a clean pre-install workflow before deploying a security platform in AWS. The server was validated for OS version, compute, memory, storage, public IP, update status, and reboot readiness before installing Wazuh. That demonstrates good operational discipline, system administration habits, and secure deployment preparation.
