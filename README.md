# Wazuh SIEM Deployment on VMware Cloud Director

> Enterprise SIEM deployment using Wazuh on VMware Cloud Director for centralized security monitoring, threat hunting, vulnerability assessment, and compliance auditing.

---

## Overview

This project demonstrates the deployment and configuration of a Wazuh SIEM environment using VMware Cloud Director and Ubuntu virtual machines. The implementation provides centralized visibility into endpoint activities and showcases key Security Operations Center (SOC) capabilities such as threat detection, vulnerability management, compliance monitoring, and security configuration assessment.

The environment was built using a simple two-node architecture consisting of one Wazuh Server and one monitored Ubuntu endpoint.

---

## Architecture

```
                Internet
                    │
                    ▼
         VMware Cloud Director
                    │
     ┌──────────────┴──────────────┐
     │                             │
     ▼                             ▼
 Wazuh Server                 Wazuh Agent
 Ubuntu 22.04                 Ubuntu 22.04
 192.168.0.2                  192.168.0.3
     │                             │
     └──────────────┬──────────────┘
                    ▼
            Wazuh Dashboard
           Threat Hunting
         MITRE ATT&CK Mapping
       Vulnerability Detection
         SCA & Compliance
```

---

## Environment Details

| Component | Details |
|------------|----------|
| Platform | VMware Cloud Director |
| Wazuh Version | 4.13.1 |
| Server OS | Ubuntu 22.04.1 LTS |
| Agent OS | Ubuntu 22.04.1 LTS |
| Wazuh Server IP | 192.168.0.2 |
| Wazuh Agent IP | 192.168.0.3 |
| Dashboard | Wazuh Dashboard |
| Compliance Standard | PCI DSS |
| Benchmark | CIS Ubuntu 22.04 |

---

## Objectives

- Deploy Wazuh Manager, Indexer, and Dashboard.
- Register and monitor Ubuntu endpoints.
- Collect and analyze security events.
- Detect authentication anomalies and SSH attacks.
- Perform vulnerability assessments.
- Conduct CIS benchmark evaluations.
- Map security events to MITRE ATT&CK techniques.
- Demonstrate compliance monitoring capabilities.

---

## Features Implemented

### Endpoint Monitoring

- Successful registration of Ubuntu endpoint agents.
- Real-time collection and analysis of endpoint events.
- Active monitoring through the Wazuh Dashboard.

### Threat Hunting

The deployment detected and visualized authentication-related events, including:

- Invalid SSH login attempts.
- Non-existent user login attempts.
- PAM authentication failures.

Example detection:

```
Rule ID: 5710
Description: sshd: Attempt to login using a non-existent user
```

### MITRE ATT&CK Mapping

Detected alerts were automatically mapped to MITRE ATT&CK techniques, including:

- Credential Access
- Lateral Movement
- Defense Evasion

### Vulnerability Detection

Wazuh successfully identified vulnerabilities affecting installed packages on the monitored endpoint.

Observed vulnerability summary:

| Severity | Count |
|-----------|---------|
| Critical | 86 |
| High | 950 |
| Medium | 696 |

### Security Configuration Assessment (SCA)

CIS Ubuntu 22.04 benchmark assessments were performed against the monitored endpoint.

Results:

| Passed | Failed | Not Applicable | Score |
|----------|----------|----------------|---------|
| 89 | 107 | 11 | 45% |

### Compliance Monitoring

Security events were mapped to PCI DSS requirements, providing visibility into compliance-related controls.

---

## Results

The implementation successfully demonstrated:

- Centralized security monitoring.
- Endpoint visibility.
- Real-time threat hunting.
- SSH authentication anomaly detection.
- MITRE ATT&CK integration.
- Vulnerability management.
- CIS benchmark auditing.
- PCI DSS compliance monitoring.
- Practical troubleshooting of SIEM deployment issues.

---

## Technologies Used

- Wazuh 4.13.1
- VMware Cloud Director
- Ubuntu 22.04 LTS
- OpenSearch
- Linux LVM
- MITRE ATT&CK Framework
- CIS Benchmarks

---

## Repository Structure

```
wazuh-siem-vmware-cloud-director/
├── README.md
├── setup-guide.md
├── LICENSE
└── Wazuh-SIEM-Project-Screenshots.pdf
```

---

## Team Members

- Shivani Sharma
- Chand Perveen
---

## Conclusion

This project demonstrates the end-to-end deployment of a Wazuh SIEM solution within VMware Cloud Director using Ubuntu endpoints. Beyond implementation, the project involved troubleshooting real-world operational challenges such as agent compatibility issues and OpenSearch storage limitations. The resulting environment provides a practical demonstration of SOC workflows including threat detection, vulnerability assessment, compliance monitoring, and security analytics.
