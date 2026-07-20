# 🚀 Step 06 - Connect macOS Wazuh Agent to AWS Wazuh Manager

## 🎯 Goal

Connect the existing macOS Wazuh Agent to the AWS-hosted Wazuh Manager.

## 🧠 What We Are Doing

Your Mac agent is already installed. So this step is **not** reinstalling Wazuh. Instead, we are:

1. Finding the macOS agent config file 🔎
2. Backing it up 💾
3. Changing the manager address to the AWS Wazuh Manager public IP 🌍
4. Restarting the Mac Wazuh Agent 🔄
5. Confirming it appears in the Wazuh Dashboard 📊

---

## 🏗️ Architecture After This Step

```text
macOS Endpoint
  ↓
Wazuh Agent
  ↓
AWS Public IP
  ↓
AWS Wazuh Manager
```

### 🧒 Kid meaning

Your Mac is the helper computer, and the AWS server is the big security office. The agent sends security messages to the manager.

---

## 🔐 Very Important: Security Group First

Since your Mac agent will reach the AWS Wazuh Manager through the AWS public IP, the Wazuh Manager security group must allow your Mac public IP.

### Required rules on the AWS Wazuh Manager security group

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 1514 | TCP | Agent sends security events to manager | My IP only |
| 1515 | TCP | Agent enrollment/registration | My IP only |

### Why these ports matter

- `1514/TCP` = agent event traffic 📡
- `1515/TCP` = agent enrollment/registration 📝

Wazuh enrollment documentation lists `1514/TCP` for agent communication and `1515/TCP` for enrollment [web:183][web:189].

### Kid meaning

- `1514` = “I have security news to send” 📬
- `1515` = “Let me in first” 🚪

Do not leave these open to `Anywhere` unless it is only temporary for testing.

---

## 🛠️ Commands Used on macOS

## 1️⃣ Confirm the config file exists

```bash
sudo ls -l /Library/Ossec/etc/ossec.conf
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator |
| `ls` | List file |
| `-l` | Long/detail view |
| `/Library/Ossec/etc/ossec.conf` | Main Wazuh agent config file on macOS |

### ✅ Purpose

Check if the Wazuh Mac agent configuration file exists.

### 👶 Kid meaning

Check if the Mac agent config file is there.

---

## 2️⃣ Back up the config file

```bash
sudo cp /Library/Ossec/etc/ossec.conf /Library/Ossec/etc/ossec.conf.backup-before-aws-manager
```

| Part | Meaning |
|---|---|
| `cp` | Copy a file |
| Source path | Original Wazuh config |
| Destination path | Backup copy |

### ✅ Purpose

Make a safety copy before editing the config.

### 👶 Kid meaning

Save a backup in case something goes wrong.

Wazuh documentation says `ossec.conf` is XML and recommends backing it up before changes because a configuration mistake can stop services from starting [web:160].

---

## 3️⃣ Edit the config file

```bash
sudo nano /Library/Ossec/etc/ossec.conf
```

| Part | Meaning |
|---|---|
| `nano` | Terminal text editor |
| `/Library/Ossec/etc/ossec.conf` | Wazuh agent config file |

### ✅ Purpose

Open the macOS Wazuh Agent config so the manager address can be updated.

### 👶 Kid meaning

Open the file so we can tell the agent where the AWS server is.

---

## 4️⃣ Find the manager address section

Inside the file, look for:

```xml
<client>
  <server>
    <address>OLD_MANAGER_IP</address>
  </server>
</client>
```

Change it to your AWS Wazuh Manager public IP:

```xml
<client>
  <server>
    <address>AWS_MANAGER_PUBLIC_IP</address>
  </server>
</client>
```

### Example format only

```xml
<client>
  <server>
    <address>1.2.3.4</address>
  </server>
</client>
```

Wazuh macOS enrollment docs say to place the manager IP or FQDN inside `<client><server><address>` so the agent can connect and automatically request a client key [web:183][web:175].

### Kid meaning

Change the “where do I report?” address to your AWS server.

---

## 5️⃣ Optional: give the Mac a clean agent name

You can add enrollment info like this:

```xml
<client>
  <server>
    <address>AWS_MANAGER_PUBLIC_IP</address>
  </server>
  <enrollment>
    <agent_name>macbook-agent</agent_name>
  </enrollment>
</client>
```

### What it means

| XML tag | Meaning |
|---|---|
| `<client>` | This endpoint’s Wazuh connection settings |
| `<server>` | The Wazuh Manager it reports to |
| `<address>` | Manager IP or DNS name |
| `<enrollment>` | Optional registration info |
| `<agent_name>` | Friendly name shown in the dashboard |

### Kid meaning

Give your Mac a nickname so it is easy to find in the dashboard.

Wazuh docs say `agent_name` is optional, and if you do not set it, Wazuh uses the endpoint hostname by default [web:183].

---

## 6️⃣ Save in nano

In Nano:

- `CTRL + O` = save 💾
- `Enter` = confirm ✅
- `CTRL + X` = exit ✨

---

## 7️⃣ Restart the Mac Wazuh Agent

```bash
sudo /Library/Ossec/bin/wazuh-control restart
```

| Part | Meaning |
|---|---|
| `sudo` | Run as administrator |
| `/Library/Ossec/bin/wazuh-control` | Wazuh control tool on macOS |
| `restart` | Stop and start the agent again |

### ✅ Purpose

Restart the Mac Wazuh agent so it reads the new AWS Manager address.

### 👶 Kid meaning

Restart the agent so it remembers the new server address.

Wazuh’s macOS docs specifically say to restart the agent after editing the config [web:183].

---

## 8️⃣ Check Mac agent status

```bash
sudo /Library/Ossec/bin/wazuh-control status
```

### ✅ Purpose

Confirm the Wazuh Agent processes are running.

### 👶 Kid meaning

Ask the agent if it is awake and working.

---

## 9️⃣ Check Mac agent logs

```bash
sudo tail -n 50 /Library/Ossec/logs/ossec.log
```

| Part | Meaning |
|---|---|
| `tail` | Show the end of a file |
| `-n 50` | Show last 50 lines |
| `/Library/Ossec/logs/ossec.log` | Wazuh Agent log file |

### ✅ Purpose

Check whether the Mac agent connected successfully to the AWS Wazuh Manager.

### 👀 Look for

- Connected to the server
- Agent started
- Authentication succeeded

### Kid meaning

Show me the latest messages from the Mac agent.

---

## 10️⃣ Verify in Wazuh Dashboard

Open:

```text
https://<AWS_MANAGER_PUBLIC_IP>
```

Then go to:

```text
Agents management → Summary
```

Look for:

- `macbook-agent`
- or your Mac hostname

Wazuh docs say to check the newly enrolled agent and its connection status in **Agents management > Summary** [web:183].

---

## 🚨 If It Does Not Connect

### Problem 1: Security group blocking Mac

Fix on AWS Wazuh Manager security group:

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 1514 | TCP | Agent event forwarding | My IP only |
| 1515 | TCP | Agent enrollment | My IP only |

### Problem 2: Wrong manager IP

Make sure the Mac config has:

```xml
<address>AWS_MANAGER_PUBLIC_IP</address>
```

### Problem 3: XML mistake

Open the file again:

```bash
sudo nano /Library/Ossec/etc/ossec.conf
```

Make sure every tag closes correctly:

- `<client>` must end with `</client>`
- `<server>` must end with `</server>`

### Problem 4: Agent not restarted

Run again:

```bash
sudo /Library/Ossec/bin/wazuh-control restart
```

---

## ✅ Checklist

- [ ] macOS config backed up
- [ ] AWS Manager public IP added to `ossec.conf`
- [ ] Optional agent name configured
- [ ] Wazuh Agent restarted
- [ ] Agent logs checked
- [ ] macOS endpoint appears in dashboard
- [ ] macOS endpoint status is Active

---

## 🗣️ Interview Explanation

This step shows how to connect an already installed macOS Wazuh Agent to an AWS-hosted Wazuh Manager by editing the local agent configuration, backing up the file first, restarting the service, and validating the connection in the dashboard. It demonstrates safe configuration management, secure agent enrollment, and endpoint monitoring setup.
