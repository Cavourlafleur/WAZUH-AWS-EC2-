# Wazuh AWS Self-Hosted Pilot

Deploy Wazuh Community Edition in AWS with one all-in-one server and agents on AWS + macOS endpoints.

## Goal
- Deploy Wazuh Community self-hosted in AWS.
- Monitor:
  - AWS EC2 endpoint(s)
  - macOS endpoint(s)
  - Future endpoints via additional Wazuh agents

## Architecture

```text
                 ┌──────────────────────────────┐
                 │ AWS EC2 #1                   │
                 │ Wazuh All-in-One Server      │
                 │                              │
                 │ - Wazuh Manager              │
                 │ - Wazuh Indexer              │
                 │ - Wazuh Dashboard            │
                 │ - Filebeat                   │
                 └──────────────▲───────────────┘
                                │
             Agents send logs/security events here
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
┌──────────────┐       ┌────────────────┐       ┌────────────────┐
│ AWS EC2 #2   │       │ Your MacBook   │       │ Future systems │
│ Wazuh Agent  │       │ Wazuh Agent    │       │ Wazuh Agents   │
└──────────────┘       └────────────────┘       └────────────────┘
```

## Manager EC2 Sizing (All-in-One)
- **vCPU:** 4
- **RAM:** 8 GiB
- **Storage:** 50 GB gp3 EBS

## Components
- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Filebeat
- Wazuh Agent (for monitored endpoints)

## Deployment Outline

### 1) AWS EC2 #1 (Wazuh All-in-One Server)
- Launch an EC2 instance sized to 4 vCPU / 8 GiB RAM.
- Attach 50 GB gp3 EBS volume.
- Install Wazuh all-in-one stack (manager, indexer, dashboard, filebeat) using official Wazuh documentation.
- Validate dashboard login and manager/indexer health.

### 2) AWS EC2 #2 (Linux endpoint with Wazuh Agent)
- Launch a second EC2 instance for endpoint monitoring.
- Install Wazuh Agent.
- Enroll/register the agent to the manager private IP or DNS.
- Confirm the agent appears as active in Wazuh Dashboard.

### 3) macOS endpoint (Wazuh Agent)
- Install Wazuh Agent on macOS.
- Configure manager address and register the agent.
- Confirm logs/events from macOS appear in the dashboard.

## Security Groups (Documented Access)

Use least privilege and restrict source IPs/CIDRs.

| Port | Protocol | Purpose | Recommended Source |
|---|---|---|---|
| 22 | TCP | SSH administration | Admin public IP only |
| 443 | TCP | Wazuh Dashboard (HTTPS) | Admin public IP only (or VPN CIDR) |
| 1514 | TCP/UDP | Agent event forwarding | Only agent subnet CIDR(s) |
| 1515 | TCP | Agent enrollment/registration | Only agent subnet CIDR(s) |

> Optional: if using the dashboard default 5601 internally, limit it to private/VPN sources only.

## Cost Controls
- Stop instances when not in use.
- Start with 50 GB storage and increase only when needed.
- Avoid allocating unused Elastic IPs.
- Create an AWS Budget alert for monthly spend.

## Pilot Validation Checklist
- [ ] All-in-one Wazuh server reachable over approved admin path only.
- [ ] EC2 agent is connected and sending events.
- [ ] macOS agent is connected and sending events.
- [ ] Security group rules limited to known source IPs/CIDRs.
- [ ] Budget alert created and tested.