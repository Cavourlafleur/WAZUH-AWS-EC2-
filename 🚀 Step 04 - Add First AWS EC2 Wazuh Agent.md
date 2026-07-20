# 🚀 Step 04 - Add First AWS EC2 Wazuh Agent

## 🎯 Goal

Create AWS EC2 #2 as a test endpoint, install the Wazuh Agent on it, connect it to the AWS Wazuh Manager, and confirm it appears in the Wazuh Dashboard.

## 🧠 What We Are Building

Right now:

- AWS EC2 #1 = Wazuh Manager / Indexer / Dashboard

Now we add:

- AWS EC2 #2 = Linux endpoint with Wazuh Agent

### Simple meaning

- Manager = security office 🏢
- Agent = security sensor on another server 📡

### Architecture after this step

```text
AWS EC2 #2
Wazuh Agent
↓ sends events
AWS EC2 #1
Wazuh Manager
↓ analyzes
Wazuh Indexer
↓ stores
Wazuh Dashboard
↓ you view alerts
```

---

## 🖥️ Launch AWS EC2 Agent Server

In AWS Console:

```text
EC2 → Instances → Launch instances
```

Use this:

- Name: `wazuh-agent-aws-test`
- OS: Ubuntu Server 22.04 or 24.04
- Instance type: `t2.micro`, `t3.micro`, or any small free/cheap option available
- Storage: default is okay, maybe 8–20 GB
- Key pair: use same key pair if you want
- Security group: create or use `wazuh-agent-sg`

### 💡 Resource note

The agent server does **not** need 8 GiB RAM like the manager. Wazuh documentation says the agent is lightweight and uses very little memory on average, around 35 MB RAM [web:39].

---

## 🔐 Agent Security Group

### Agent EC2 security group

Inbound rules:

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 22 | TCP | SSH admin access | My IP only |

### Manager EC2 security group

Add or confirm:

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 1514 | TCP | Agent communication | AWS agent private IP or agent security group |
| 1515 | TCP | Agent enrollment | AWS agent private IP or agent security group |

### ELI10

The agent must be allowed to knock on the manager’s Wazuh doors.

- `1515` = registration door 📝
- `1514` = daily reporting door 📬

Wazuh documentation lists `1514/TCP` for agent communication and `1515/TCP` for enrollment [web:39].

---

## 🛠️ Commands Used on AWS Agent EC2

### 1️⃣ SSH into the AWS Agent EC2

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@<AGENT_EC2_PUBLIC_IP>
```

### Purpose

Log into the AWS EC2 endpoint that will run the Wazuh Agent.

### Kid meaning

Use my key to enter the new AWS endpoint server.

---

### 2️⃣ Update Ubuntu Package List

```bash
sudo apt update
```

### Purpose

Refresh available package and security update information.

### Kid meaning

Ask Ubuntu what software updates exist.

---

### 3️⃣ Upgrade Installed Packages

```bash
sudo apt upgrade -y
```

### Purpose

Update the endpoint before installing Wazuh Agent.

### Kid meaning

Install available updates before adding Wazuh Agent.

---

### 4️⃣ Install Required Packages

```bash
sudo apt install -y curl gnupg apt-transport-https
```

### Purpose

Install the tools needed to safely add the Wazuh repository.

### Kid meaning

Install tools that help Ubuntu safely download Wazuh packages.

Wazuh Linux agent docs show `gnupg` and `apt-transport-https` before adding the Wazuh repository [web:39].

---

### 5️⃣ Add the Wazuh Package Signing Key

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import
```

### Purpose

Tell Ubuntu to trust packages signed by Wazuh.

### Kid meaning

Tell Ubuntu that Wazuh packages are safe to trust.

Wazuh docs show importing the Wazuh GPG key into `/usr/share/keyrings/wazuh.gpg` [web:39].

### Set the key permissions

```bash
sudo chmod 644 /usr/share/keyrings/wazuh.gpg
```

### Purpose

Set the key file permissions so Ubuntu can read it correctly.

### Kid meaning

Make the key readable so Ubuntu can use it.

Wazuh docs include setting the Wazuh key file permission to `644` [web:39].

---

### 6️⃣ Add the Wazuh Repository

```bash
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee -a /etc/apt/sources.list.d/wazuh.list
```

### Purpose

Add the Wazuh software repository to Ubuntu.

### Kid meaning

Add the Wazuh software store to Ubuntu.

Wazuh docs show adding this Wazuh APT repository line [web:39].

### Update package list again

```bash
sudo apt update
```

### Purpose

Refresh Ubuntu package information after adding the Wazuh repository.

### Kid meaning

Refresh Ubuntu again so it can see the new Wazuh packages.

---

### 7️⃣ Install Wazuh Agent

You need the Wazuh Manager private IP address if both EC2s are in the same VPC.

Example:

```text
172.31.x.x
```

Install command:

```bash
sudo WAZUH_MANAGER="<WAZUH_MANAGER_PRIVATE_IP>" WAZUH_AGENT_NAME="aws-agent-test" apt-get install -y wazuh-agent
```

Example:

```bash
sudo WAZUH_MANAGER="172.31.10.25" WAZUH_AGENT_NAME="aws-agent-test" apt-get install -y wazuh-agent
```

### Purpose

Install the Wazuh Agent and tell it which manager to report to.

### Kid meaning

Install the agent and point it to the manager so it knows where to send data.

Wazuh docs say to replace `WAZUH_MANAGER` with the manager IP address or hostname when deploying the Linux agent [web:39].

---

### 8️⃣ Start the Wazuh Agent Service

```bash
sudo systemctl daemon-reload
```

### Purpose

Reload Linux service definitions.

### Kid meaning

Tell Linux to refresh its service list.

```bash
sudo systemctl enable wazuh-agent
```

### Purpose

Make Wazuh Agent start automatically when the server reboots.

### Kid meaning

Make the agent turn on by itself after a restart.

```bash
sudo systemctl start wazuh-agent
```

### Purpose

Turn on the Wazuh Agent right now.

### Kid meaning

Turn the Wazuh Agent on now.

Wazuh docs show enabling and starting the agent service with `systemctl daemon-reload`, `systemctl enable wazuh-agent`, and `systemctl start wazuh-agent` [web:39].

---

### 9️⃣ Check Agent Service Status

```bash
sudo systemctl status wazuh-agent --no-pager
```

### Purpose

Confirm the Wazuh Agent service is running.

### What you want to see

```text
active (running)
```

---

### 🔟 Check Agent Log if Needed

```bash
sudo tail -n 50 /var/ossec/logs/ossec.log
```

### Purpose

Show the latest Wazuh Agent messages.

### Kid meaning

Show me the latest Wazuh Agent messages.

---

### 1️⃣1️⃣ Verify in Wazuh Dashboard

Go to your Wazuh Dashboard:

```text
https://<WAZUH_MANAGER_PUBLIC_IP>
```

Then:

```text
Agents management → Summary
```

Look for:

```text
aws-agent-test
```

If it says **Active**, success 🎉

Wazuh documentation also says you can deploy a new agent from the dashboard by going to Agents management > Summary and clicking Deploy new agent [web:39].

---

## 🚫 Important: Do Not Move to Mac Agent Yet

First confirm:

- AWS Agent connected successfully ✅
- Agent appears in the dashboard ✅
- Status is Active ✅

Then the Mac agent can be connected later. Keeping this step separate makes troubleshooting easier.

---

## ✅ Checklist

- [ ] AWS EC2 agent instance launched
- [ ] SSH to agent instance works
- [ ] Agent server updated
- [ ] Wazuh repository added
- [ ] Wazuh Agent installed
- [ ] Wazuh Agent service started
- [ ] Agent appears in dashboard
- [ ] Agent status is active

---

## 🗣️ Interview Explanation

This step shows how to deploy a Wazuh Agent on an AWS EC2 Linux endpoint and connect it to a central Wazuh Manager. It also shows secure package installation, repository trust setup, service management, and dashboard validation. That demonstrates endpoint onboarding, Linux administration, and SIEM deployment skills.
