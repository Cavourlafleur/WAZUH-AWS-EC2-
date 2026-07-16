# 🔐 Step 02 - SSH Access to AWS Wazuh Manager

## 🎯 Goal

Connect from macOS Terminal to the AWS EC2 instance that will host the Wazuh Manager.

---

## 🖥️ EC2 Role

This EC2 instance will become the Wazuh all-in-one server. Later, it will run:

- Wazuh Manager 🛡️
- Wazuh Indexer 📚
- Wazuh Dashboard 📊
- Filebeat 📦

---

## 📌 Project Rule

For this stage of the project, the connection method is:

- **Main AWS admin access** = SSH to the AWS public IP 🌍
- **Mac agent connectivity** = Tailscale can be discussed later 🔄
- **For now** = SSH only 🔐

---

## 🗝️ SSH Key Pair

AWS uses a key pair for Linux SSH access. The private key stays on the admin machine and is used to prove identity when connecting to the EC2 instance. AWS stores the public key on the instance, and the matching private key must be provided during SSH login [web:59].

### 📄 Example key file

```text
wazuh-keypair.pem
```

---

## 🛠️ Commands Used

## 1️⃣ Find the Key File in Downloads

```bash
ls ~/Downloads
```

| Part | Meaning |
|---|---|
| `ls` | List files and folders |
| `~/Downloads` | Downloads folder on macOS |

### ✅ Purpose

Find the downloaded AWS private key file.

### 👶 Kid meaning

Show me what files are inside my Downloads folder.

### 🔍 Look for

```text
wazuh-keypair.pem
```

---

## 2️⃣ Create the SSH Folder

```bash
mkdir -p ~/.ssh
```

| Part | Meaning |
|---|---|
| `mkdir` | Make a new folder |
| `-p` | Do not complain if the folder already exists |
| `~/.ssh` | Hidden SSH folder inside the user home directory |

### ✅ Purpose

Create a safe location to store SSH keys.

### 👶 Kid meaning

Create my secret SSH key folder if it does not already exist.

---

## 3️⃣ Move the AWS Key Into the SSH Folder

```bash
mv ~/Downloads/wazuh-keypair.pem ~/.ssh/
```

| Part | Meaning |
|---|---|
| `mv` | Move a file |
| `~/Downloads/wazuh-keypair.pem` | The AWS private key in Downloads |
| `~/.ssh/` | Destination SSH folder |

### ✅ Purpose

Move the AWS private key into the standard SSH folder for safer storage and easier use.

### 👶 Kid meaning

Move my AWS key from Downloads into my SSH key folder.

---

## 4️⃣ Lock the Private Key Permissions

```bash
chmod 400 ~/.ssh/wazuh-keypair.pem
```

| Part | Meaning |
|---|---|
| `chmod` | Change file permissions |
| `400` | Only the owner can read the file |
| `~/.ssh/wazuh-keypair.pem` | The AWS private key file |

### ✅ Purpose

Protect the key so SSH will accept it. SSH may reject a private key file if the permissions are too open.

### 👶 Kid meaning

Lock the key so only I can read it.

---

## 5️⃣ Get the EC2 Public IP

In the AWS Console, go here:

```text
EC2 -> Instances -> wazuh-manager -> Public IPv4 address
```

### 🌍 Example format

```text
3.88.123.45
```

> ⚠️ Do not post your real public IP publicly.

---

## 6️⃣ SSH Into the AWS Server

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@<EC2_PUBLIC_IP>
```

### 🧪 Example format

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@3.88.123.45
```

| Part | Meaning |
|---|---|
| `ssh` | Securely connect to a remote Linux server |
| `-i` | Use this identity/private key file |
| `~/.ssh/wazuh-keypair.pem` | Your AWS private key |
| `ubuntu` | Default username for Ubuntu EC2 |
| `@` | Connect as this user to this server |
| `<EC2_PUBLIC_IP>` | Public IP of the EC2 instance |

### ✅ Purpose

Log into the AWS Ubuntu server using the correct private key.

### 👶 Kid meaning

Use my AWS key to log into the Ubuntu server in AWS.

AWS documents SSH as the standard method for connecting to Linux instances, using the matching private key for authentication [web:36].

---

## 7️⃣ First-Time Fingerprint Prompt

The first time you connect, you may see:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Type:

```text
yes
```

### ✅ Meaning

Your Mac is asking if you trust this server and want to remember it.

### 👶 Kid meaning

My computer is saying: I have never met this server before. Should I trust it?

Since this is your own new EC2 instance, typing `yes` is normal ✅

---

## 8️⃣ Successful Login

If SSH works, you may see something like:

```text
Welcome to Ubuntu
ubuntu@ip-172-31-xx-xx:~$
```

### 🎉 Meaning

You are now inside the AWS Ubuntu server.

This is a **big milestone** 🚀

---

## 🚨 Troubleshooting

## ❌ Error 1 - Permission Denied (publickey)

```text
Permission denied (publickey)
```

### Possible causes

- Wrong key file
- Wrong username
- Wrong EC2 instance
- Key permissions were not locked correctly

### ✅ Fix checklist

- Make sure the key file is correct
- Make sure the username is `ubuntu`
- Make sure the file exists in `~/.ssh`
- Re-run:

```bash
chmod 400 ~/.ssh/wazuh-keypair.pem
```

---

## ❌ Error 2 - Operation Timed Out

```text
Operation timed out
```

### Possible causes

- Security group does not allow SSH on port 22
- Wrong public IP
- Instance is stopped
- Local IP changed
- Network issue

### ✅ Security group fix

```text
Port: 22/TCP
Source: My IP
```

AWS security groups control inbound and outbound traffic for EC2 instances, and inbound rules determine what traffic can reach the instance [web:51].

---

## ❌ Error 3 - Unprotected Private Key File

```text
WARNING: UNPROTECTED PRIVATE KEY FILE!
```

### ✅ Fix

```bash
chmod 400 ~/.ssh/wazuh-keypair.pem
```

This locks the key again so SSH will accept it.

---

## 🔐 Security Group Reminder

At this stage, your AWS Security Group should look something like this:

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 22 | TCP | SSH admin access | My IP only |
| 443 | TCP | Wazuh Dashboard | My IP only |
| 1514 | TCP | Agent traffic (temporary) | My IP only |
| 1515 | TCP | Agent enrollment (temporary) | My IP only |

### ✅ Recommended

```text
22/TCP from My IP only
```

### ❌ Avoid

```text
22/TCP from Anywhere / 0.0.0.0/0
```

Leaving SSH open to the whole internet allows anyone to attempt login against the server 😬

---

## ✅ Status Checklist

- [ ] Key file found in Downloads
- [ ] SSH folder created
- [ ] Key moved into `~/.ssh`
- [ ] Key permissions changed to `400`
- [ ] EC2 public IP copied
- [ ] SSH login successful
- [ ] Ubuntu shell prompt confirmed

---

## ⏭️ Next Step

Once SSH works, the next step is to run AWS Ubuntu pre-install checks before installing Wazuh.

---

## 🗣️ Interview Explanation

This step demonstrates secure remote administration of an AWS-hosted Ubuntu server using SSH key-based authentication. It also shows awareness of private key protection, least-exposure network access through security groups, and safe cloud administration practices.
