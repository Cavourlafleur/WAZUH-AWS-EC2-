# Step 02 - SSH Access to AWS Wazuh Manager

## Goal

Connect from macOS Terminal to the AWS EC2 instance that will host the Wazuh Manager.

## EC2 Role

This EC2 instance will become the Wazuh all-in-one server. It will later run:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Filebeat

## Project Rule

For this stage of the project, the connection method is fixed:

- **Main AWS admin access** = SSH to the AWS EC2 public IP
- **Mac agent connectivity** = Tailscale can be discussed later if needed
- **For now** = SSH only

## SSH Key Pair

AWS uses a key pair for Linux SSH access. The private key file stays on the admin machine and proves your identity to the instance. AWS keeps the public key on the instance, and you keep the private key file [web:1].

Example key file:

```text
wazuh-keypair.pem
```

---

## Commands Used

### 1. List Downloads Folder

```bash
ls ~/Downloads
```

| Part | Meaning |
|---|---|
| `ls` | List files and folders |
| `~/Downloads` | Downloads folder on macOS |

**Purpose:** Find the AWS private key file that was downloaded when the key pair was created.

**Kid explanation:** Show me what files are inside my Downloads folder.

---

### 2. Create the SSH Folder

```bash
mkdir -p ~/.ssh
```

| Part | Meaning |
|---|---|
| `mkdir` | Create a folder |
| `-p` | Do not error if the folder already exists |
| `~/.ssh` | Hidden SSH folder in the home directory |

**Purpose:** Create a safe, standard location to store SSH keys.

**Kid explanation:** Create a safe SSH key folder if it does not already exist.

---

### 3. Move the AWS Key Into the SSH Folder

```bash
mv ~/Downloads/wazuh-keypair.pem ~/.ssh/
```

| Part | Meaning |
|---|---|
| `mv` | Move a file |
| `~/Downloads/wazuh-keypair.pem` | The key file currently in Downloads |
| `~/.ssh/` | Destination SSH folder |

**Purpose:** Move the private key out of Downloads and into the secure SSH folder.

**Kid explanation:** Move my AWS key from Downloads into my SSH key folder.

---

### 4. Lock the Private Key Permissions

```bash
chmod 400 ~/.ssh/wazuh-keypair.pem
```

| Part | Meaning |
|---|---|
| `chmod` | Change file permissions |
| `400` | Only the owner can read the file, no one can write or execute it |
| `~/.ssh/wazuh-keypair.pem` | The AWS private key file |

**Purpose:** Protect the private key so SSH will actually accept using it. SSH refuses to use a key file that has permissions open to others.

**Kid explanation:** Lock the key so only I can read it.

---

### 5. Get the EC2 Public IP

In the AWS Console:

```text
EC2 -> Instances -> wazuh-manager -> Copy Public IPv4 address
```

Example format only (do not share your real IP publicly):

```text
3.88.123.45
```

---

### 6. SSH Into the EC2 Instance

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@<EC2_PUBLIC_IP>
```

Example with a placeholder IP:

```bash
ssh -i ~/.ssh/wazuh-keypair.pem ubuntu@3.88.123.45
```

| Part | Meaning |
|---|---|
| `ssh` | Securely connect to a remote Linux server |
| `-i` | Use this identity/private key file |
| `~/.ssh/wazuh-keypair.pem` | The AWS private key |
| `ubuntu` | Default username for Ubuntu EC2 instances |
| `@` | Means "connect as this user to this server" |
| `<EC2_PUBLIC_IP>` | The server's public address |

**Purpose:** Log into the AWS Ubuntu server using the private key as proof of identity.

**Kid explanation:** Use my AWS key to log into the Ubuntu server in AWS.

---

### 7. First-Time Fingerprint Prompt

On first connection, you may see:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Type:

```text
yes
```

**Meaning:** Your Mac is asking, "I have never met this server before — should I trust and remember it?" Since you just created this EC2 instance yourself, it's safe to type `yes`.

---

### 8. Successful Login

If everything worked, you'll see something like:

```text
Welcome to Ubuntu
ubuntu@ip-172-31-xx-xx:~$
```

This means you are now inside the AWS Wazuh Manager server.

---

## Security Group Reminder

AWS security groups control inbound and outbound traffic. Inbound rules control incoming traffic to your instance [web:2].

Recommended rules for this stage:

| Port | Protocol | Purpose | Source |
|---|---|---|---|
| 22 | TCP | SSH admin access | My IP only |
| 443 | TCP | Wazuh Dashboard | My IP only |
| 1514 | TCP | Agent data (temporary) | My IP only |
| 1515 | TCP | Agent enrollment (temporary) | My IP only |

**Avoid this setting:**

```text
Source: Anywhere (0.0.0.0/0)
```

Leaving SSH open to `0.0.0.0/0` allows anyone on the internet to attempt to log in. Only use this temporarily if absolutely necessary, and switch back to "My IP" as soon as possible.

---

## Troubleshooting

### Error: Permission Denied (publickey)

```text
Permission denied (publickey)
```

Possible causes:

- Wrong key file specified in the `-i` flag
- Wrong username (should be `ubuntu` for Ubuntu EC2 instances)
- Key permissions not locked to `400`
- Connecting to the wrong EC2 instance

### Error: Operation Timed Out

```text
Operation timed out
```

Possible causes:

- Security group does not allow inbound traffic on port 22
- Wrong public IP address used
- Instance is stopped, not running
- Your local IP address changed since the rule was created
- General network connectivity issue

**Fix the security group:**

```text
Port: 22/TCP
Source: My IP
```

### Error: Unprotected Private Key File

```text
WARNING: UNPROTECTED PRIVATE KEY FILE!
```

Fix by re-locking the key permissions:

```bash
chmod 400 ~/.ssh/wazuh-keypair.pem
```

---

## Status Checklist

- [ ] Key file found in Downloads
- [ ] Key moved to `~/.ssh`
- [ ] Key permissions changed to `400`
- [ ] EC2 public IP copied from AWS Console
- [ ] SSH login successful
- [ ] Ubuntu shell prompt confirmed
