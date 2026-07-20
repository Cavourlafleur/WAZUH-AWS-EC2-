# 🚀 Step 01 Wazuh Deployment Options: Community-Based vs Vendor-Based

## 🎯 Purpose of This Document

This document explains the two main Wazuh deployment options being considered for offering endpoint security monitoring as a service to clients:

- **Community-Based Wazuh** — self-hosted and managed internally 🛠️
- **Vendor-Based Wazuh** — managed through Wazuh Cloud ☁️

The goal is to help management understand:

- Which option gives more control
- Which option creates more operational responsibility
- Which option is easier to sell and support
- What clients should expect
- How Wazuh can be used to build a paid security monitoring service

---

## 🧠 Executive Summary

Wazuh is a free and open source security platform that provides SIEM and XDR capabilities for endpoints and cloud workloads [cite:216][cite:41]. Wazuh documentation says the platform includes central components such as the Wazuh server, indexer, and dashboard, while endpoints use the Wazuh agent to send data to the central platform [cite:217][cite:205].

For this business model, there are two main choices:

| Option | Meaning | Best fit |
|---|---|---|
| Community-Based Wazuh | We install, host, secure, maintain, and operate Wazuh ourselves | More control, lower software cost, higher responsibility |
| Vendor-Based Wazuh | Wazuh Cloud hosts and manages the central platform | Faster setup, less infrastructure work, vendor-managed backend |

Recommended path:

- Start with **Community-Based Wazuh** in the lab so the platform is fully understood.
- For paying clients, choose **Community-Based** or **Vendor-Based** depending on budget, compliance needs, data ownership preference, and support expectations.

---

## 🛡️ What Wazuh Does

Wazuh monitors and protects endpoints, servers, cloud systems, and workloads by collecting security data from agents, analyzing events, generating alerts, and presenting dashboards for investigation and reporting [cite:205][cite:217].  
The agent is lightweight and multi-platform, so it can be deployed to laptops, desktops, servers, cloud instances, containers, and virtual machines [cite:205][cite:218].

### Simple explanation for non-technical people

Think of Wazuh like a security monitoring center:

- The **Wazuh agent** is the security sensor installed on a laptop, server, or cloud machine 📡
- The **Wazuh manager/server** is the brain that receives and analyzes data 🧠
- The **Wazuh indexer** stores and searches alert data 📚
- The **Wazuh dashboard** is the web interface where security alerts and reports are viewed 📊

---

## 🏗️ Option 1: Community-Based Wazuh

### What Community-Based means

Community-Based Wazuh means using the open source Wazuh platform and managing the deployment internally. Wazuh documentation states that the central components can be installed on a single host or distributed across separate hosts, depending on the environment [cite:41][cite:217].

It can be hosted in:

- Our AWS account
- A client AWS account
- On-premises
- A virtual machine
- Another cloud environment

### Community-Based architecture example

```text
Client Endpoints
│
├── Windows laptops
├── macOS laptops
├── Linux servers
└── AWS EC2 instances
│
▼
Community-Based Wazuh Platform
│
├── Wazuh Manager / Server
├── Wazuh Indexer
└── Wazuh Dashboard
```

### Community-Based pros

| Pro | Explanation |
|---|---|
| More control | Infrastructure, rules, dashboards, retention, and security settings are under our control. |
| Lower software licensing cost | Wazuh is free and open source [cite:41][cite:216]. |
| Strong learning value | Managing the platform internally builds deep understanding. |
| Flexible deployment | Components can run on one host or multiple hosts [cite:217]. |
| Better service ownership | A managed security service can be built around the platform. |
| More customization | Custom rules, reports, workflows, and integrations can be created. |

### Community-Based cons

| Con | Explanation |
|---|---|
| Higher operational responsibility | Installation, updates, backups, troubleshooting, and uptime are internal responsibilities. |
| Must secure the platform | Dashboards, APIs, credentials, and backups must be protected carefully. |
| Manual maintenance | Upgrades, testing, and storage monitoring must be handled internally. |
| Scaling is our job | More endpoints require internal capacity planning. |
| Support depends on our skill | Breaks and failures must be handled internally unless outside support is used. |
| More risk if poorly managed | A badly secured Wazuh server can become a security risk. |

### What we must manage

| Area | Responsibility |
|---|---|
| Installation | Install Wazuh manager, indexer, dashboard, and agents |
| Server security | Harden SSH, firewall, dashboard, API, and access controls |
| Updates | Test and apply Wazuh and operating system updates |
| Backups | Back up configuration, certificates, and important data |
| Storage | Monitor disk usage and log retention |
| Performance | Watch CPU, RAM, indexing, and dashboard responsiveness |
| Agent onboarding | Install and enroll agents on client endpoints |
| Rules | Tune default rules and create custom rules if needed |
| Reporting | Generate client reports and compliance-support summaries |
| Troubleshooting | Fix agent, manager, dashboard, indexer, and network issues |

### Best fit

Community-Based Wazuh is best when we want maximum control and are prepared to manage the platform ourselves. It is a strong option for building a managed security service, keeping software cost low, and supporting custom rules and integrations [cite:41][cite:217].

---

## ☁️ Option 2: Vendor-Based Wazuh

### What Vendor-Based means

Vendor-Based Wazuh means using **Wazuh Cloud**, the managed service operated by Wazuh. Wazuh documentation says Wazuh Cloud hosts and manages the central components, and includes provisioning, automated scaling, ongoing updates, and operational management [cite:216].

### Vendor-Based architecture example

```text
Client Endpoints
│
├── Windows laptops
├── macOS laptops
├── Linux servers
└── AWS EC2 instances
│
▼
Wazuh Cloud
│
├── Vendor-managed Wazuh central components
├── Vendor-managed updates
├── Vendor-managed platform monitoring
└── Client/us-managed agents and response process
```

### Vendor-Based pros

| Pro | Explanation |
|---|---|
| Less backend work | Wazuh manages the central platform [cite:216]. |
| Faster setup | Wazuh Cloud is presented as ready to use with no extra hardware or software required [cite:216]. |
| Vendor-managed updates | Version upgrades and service updates are handled by Wazuh [cite:216]. |
| Managed scalability | Scaling is part of the managed service [cite:216]. |
| Platform monitoring and support | Platform monitoring and technical support are included [cite:216]. |
| Compliance-focused service | Wazuh Cloud documentation states PCI DSS and SOC 2 compliance [cite:216]. |

### Vendor-Based cons

| Con | Explanation |
|---|---|
| Vendor cost | Wazuh Cloud is a paid managed service. |
| Less infrastructure control | The vendor manages the backend platform. |
| Shared responsibility remains | Agent deployment, rules, access control, integrations, and incident response still require internal or client involvement [cite:216]. |
| Margin pressure | Vendor cost must be included in pricing. |
| Data location questions | Some clients may ask where data is stored and who manages it. |

### Shared responsibility model

Wazuh documentation says Wazuh Cloud manages hosting, deployment, maintenance of central components, infrastructure monitoring, scaling, high availability, platform security, updates, and version upgrades [cite:216].  
Wazuh also says customers manage agent deployment/configuration, custom detection rules, alerting policies, integrations, user access control, and incident response [cite:216].

| Responsibility | Vendor-Based Wazuh |
|---|---|
| Central platform hosting | Wazuh |
| Backend updates | Wazuh |
| Platform scaling | Wazuh |
| Platform monitoring | Wazuh |
| Agent installation | Internal team or client |
| Alert review | Internal team or client |
| Incident response | Internal team or client |
| Client reporting | Internal team |
| Custom rules | Internal team or client |
| User access management | Internal team or client |

### Best fit

Vendor-Based Wazuh is best when faster deployment, less backend maintenance, and a cleaner operational model matter most. It is attractive when the client can afford a managed platform and prefers vendor-managed updates, scaling, and platform health monitoring [cite:216].

---

## 📊 Side-by-Side Comparison

| Category | Community-Based Wazuh | Vendor-Based Wazuh |
|---|---|---|
| Who hosts Wazuh? | Internal team or client | Wazuh Cloud [cite:216] |
| Who manages central components? | Internal team | Wazuh [cite:216] |
| Who installs agents? | Internal team or client | Internal team or client [cite:216] |
| Who handles backend updates? | Internal team | Wazuh [cite:216] |
| Who handles backups? | Internal team | Managed platform model; exact backup terms should be confirmed in the chosen plan |
| Who handles scaling? | Internal team | Wazuh Cloud managed service [cite:216] |
| Who reviews alerts? | Internal team or client | Internal team or client |
| Who responds to incidents? | Internal team or client | Internal team or client |
| Software cost | Lower software cost, but infrastructure and labor costs remain [cite:41] | Paid vendor service |
| Operational workload | High | Lower |
| Control | High | Lower |
| Speed to deploy | Medium | Faster |
| Best for | Building an internally managed service | Reducing backend responsibility |

---

## ✅ Compliance Considerations

Wazuh supports compliance visibility and reporting. Wazuh documentation says its default ruleset supports PCI DSS, HIPAA, NIST 800-53, TSC, and GDPR-related visibility use cases [cite:206][cite:207].  
Wazuh also says it helps with compliance support through automation, improved security controls, log analysis, and incident response, but that this does not replace full compliance by itself [cite:206][cite:207].

### Safe wording for clients

> Wazuh supports compliance visibility and reporting by mapping security events and alerts to common frameworks. It helps provide monitoring and evidence, but full compliance also depends on business policies, procedures, and operational controls. [cite:206][cite:207]

---

## 💼 Business Model Considerations

The commercial value is not limited to software installation. A Wazuh service can include deployment, configuration, agent onboarding, monitoring, alert review, reporting, maintenance, security guidance, and compliance-support visibility.

### Community-Based service model

| Service | Description |
|---|---|
| Initial setup | Install and configure Wazuh |
| Agent deployment | Install agents on endpoints |
| Cloud setup | Configure AWS/network/security groups |
| Dashboard setup | Configure dashboards and views |
| Hardening | Secure the Wazuh server and access |
| Monthly monitoring | Review alerts and endpoint health |
| Monthly reporting | Provide client reports |
| Maintenance | Updates, backups, and health checks |
| Custom rules | Create rules for client-specific needs |

### Vendor-Based service model

| Service | Description |
|---|---|
| Wazuh Cloud onboarding | Help client start with Wazuh Cloud |
| Agent deployment | Install agents on endpoints |
| Rule tuning | Tune alerts for the client environment |
| Dashboard usage | Help client understand dashboards |
| Alert review | Review and explain alerts |
| Monthly reporting | Provide client reports |
| Incident guidance | Help client respond to alerts |
| Compliance-support reporting | Organize evidence and reports |

---

## 🧩 Client Expectations

### With Community-Based Wazuh

Clients should expect:

- More flexible customization
- Potentially lower platform cost
- More responsibility handled internally
- A stronger need for careful maintenance and security
- Possibly longer setup time
- Internal ownership of platform reliability

### With Vendor-Based Wazuh

Clients should expect:

- Faster platform availability
- Less backend infrastructure responsibility
- Vendor-managed central components
- Continued need for agent management, rule tuning, alert review, and reporting on the service side [cite:216]
- Higher direct platform cost than using only the free open-source software

---

## ⚠️ Risk Comparison

| Risk | Community-Based | Vendor-Based |
|---|---|---|
| Server downtime | Must be prevented and fixed internally | Vendor-managed platform reduces backend burden |
| Updates break the system | Testing and change management remain internal | Vendor handles central platform updates [cite:216] |
| Storage fills up | Must be monitored and managed internally | Vendor-managed platform handles central infrastructure |
| Client data exposure | Hosted data must be secured internally | Vendor handles platform security, but access still must be managed [cite:216] |
| Alert overload | Rules must still be tuned internally | Rules must still be tuned internally |
| Incident response confusion | A process must be defined internally | A process still must be defined internally |
| Profit margin pressure | Labor and infrastructure costs can grow | Vendor cost affects pricing |

---

## 🏁 Recommendation

For learning, internal proof of concept, and team capability building, Community-Based Wazuh is the stronger starting point because it creates hands-on understanding of installation, architecture, agents, rules, logs, dashboards, hardening, and troubleshooting [cite:41][cite:212].  
For a first paying client, the choice should follow the client’s priorities.

| Client priority | Recommended option |
|---|---|
| Lowest vendor cost | Community-Based |
| Maximum control and customization | Community-Based |
| Fastest deployment | Vendor-Based |
| Less infrastructure responsibility | Vendor-Based |
| Client wants full backend management | Community-Based, if operational readiness exists |
| Client wants vendor-managed backend | Vendor-Based |
| Strict data and control requirements | Case-by-case review |

## Boss-friendly summary

There are two realistic Wazuh options. Community-Based Wazuh means hosting and managing the platform directly, which provides more control and can support a strong managed service offering, but also creates responsibility for updates, backups, security, storage, uptime, and troubleshooting [cite:41]. Vendor-Based Wazuh through Wazuh Cloud reduces infrastructure work and speeds up deployment because the vendor manages the central platform, but it introduces vendor cost and still leaves agent management, alert review, reporting, and incident response on the service side [cite:216].

## Decision framework

Before choosing a model, ask:

- Does the client want the lowest possible platform cost?
- Does the client want less backend responsibility?
- Is there readiness to manage Wazuh in production?
- Does the client require vendor-managed platform support?
- Is maximum control and customization a priority?
- Is faster deployment with less infrastructure management the main goal?

## Final recommendation

For the current project, the strongest path is to start with Community-Based Wazuh for lab work and platform mastery [cite:41][cite:212]. For client services, the strongest commercial approach is to offer both Community-Based Managed Wazuh and Vendor-Based Wazuh Cloud management, because the real value is not only the platform itself but also planning, deployment, agent onboarding, hardening, alert review, rule tuning, reporting, compliance-support visibility, client guidance, and ongoing management.
