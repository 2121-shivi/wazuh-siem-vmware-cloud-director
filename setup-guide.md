# Wazuh Setup Guide

This guide documents the steps followed to deploy Wazuh SIEM on VMware Cloud Director using Ubuntu 22.04 virtual machines.

---

## Environment

| Component | Details |
|------------|----------|
| Platform | VMware Cloud Director |
| Wazuh Version | 4.13.1 |
| Server OS | Ubuntu 22.04.1 LTS |
| Agent OS | Ubuntu 22.04.1 LTS |
| Wazuh Server IP | 192.168.0.2 |
| Wazuh Agent IP | 192.168.0.3 |

---

# 1. Deploy Virtual Machines

## Wazuh Server

- Ubuntu 22.04.1 LTS
- 4 vCPUs
- 8 GB RAM
- 90 GB Disk

## Wazuh Agent

- Ubuntu 22.04.1 LTS
- 4 vCPUs
- 8 GB RAM
- 37 GB Disk

---

# 2. Verify Connectivity

From the Agent VM:

```bash
ping 192.168.0.2
```

From the Server VM:

```bash
ping 192.168.0.3
```

---

# 3. Install Wazuh Server

Update the server:

```bash
apt update && apt upgrade -y
```

Download the Wazuh installation assistant:

```bash
curl -sO https://packages.wazuh.com/4.13/wazuh-install.sh
```

Make it executable:

```bash
chmod +x wazuh-install.sh
```

Install the Wazuh server:

```bash
./wazuh-install.sh -a
```

After installation, note down the generated dashboard credentials.

Example:

User: admin

Password: <generated_password>

---

# 4. Verify Wazuh Services

Check the manager:

```bash
systemctl status wazuh-manager
```

Check the indexer:

```bash
systemctl status wazuh-indexer
```

Check the dashboard:

```bash
systemctl status wazuh-dashboard
```

---

# 5. Install Wazuh Agent

Update the agent VM:

```bash
apt update
```

Install the required Wazuh agent version:

```bash
WAZUH_MANAGER="192.168.0.2" apt install wazuh-agent=4.13.1-1 -y
```

Enable the agent:

```bash
systemctl enable wazuh-agent
```

---

# 6. Configure the Agent

Edit the configuration file:

```bash
nano /var/ossec/etc/ossec.conf
```

Update the server section:

```xml
<server>
    <address>192.168.0.2</address>
    <port>1514</port>
    <protocol>tcp</protocol>
</server>
```

Save and exit.

---

# 7. Register the Agent

Register the endpoint with the manager:

```bash
/var/ossec/bin/agent-auth -m 192.168.0.2
```

Restart the agent:

```bash
systemctl restart wazuh-agent
```

Verify:

```bash
systemctl status wazuh-agent
```

The agent should appear as Active in the dashboard.

---

# 8. Verify Wazuh Ports

On the Server:

```bash
ss -tulnp | grep 1514
```

Expected:

```text
wazuh-remoted
```

Check authentication daemon:

```bash
ss -tulnp | grep 1515
```

Expected:

```text
wazuh-authd
```

---

# 9. Threat Hunting Validation

Open the Wazuh Dashboard:

```
https://<dashboard-ip>
```

Navigate to:

```
Threat Hunting → Events
```

Example detections observed:

- PAM authentication failures
- Invalid SSH users
- Non-existent user login attempts

Example rule:

Rule ID: 5710

Description:

```
sshd: Attempt to login using a non-existent user
```

---

# 10. Security Configuration Assessment

Navigate to:

```
Endpoints → Agent → Configuration Assessment
```

Observed CIS Benchmark Results:

- Passed: 89
- Failed: 107
- Not Applicable: 11
- Score: 45%

---

# 11. Vulnerability Detection

Navigate to:

```
Endpoints → Agent → Vulnerability Detection
```

Observed Results:

- Critical: 86
- High: 950
- Medium: 696

---

# 12. Challenges Encountered

## Agent–Manager Version Mismatch

Issue:

```
Agent version must be lower or equal to manager version.
```

Resolution:

Installed Wazuh Agent version 4.13.1 to match the server.

---

## Duplicate Agent Registration

Issue:

```
Duplicate agent name.
```

Resolution:

Removed existing agent entry and re-registered the endpoint.

---

## OpenSearch Flood-Stage Watermark

Issue:

```
index blocked by flood-stage watermark
```

Resolution:

- Expanded VMware virtual disk.
- Extended Ubuntu LVM storage.
- Removed OpenSearch read-only mode.
- Restarted Wazuh services.

---

# 13. Extend Ubuntu LVM After VMware Disk Expansion

Verify the disk:

```bash
lsblk
```

Expand the partition:

```bash
apt install cloud-guest-utils -y
growpart /dev/sda 3
```

Resize the physical volume:

```bash
pvresize /dev/sda3
```

Extend the logical volume:

```bash
lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```

Resize the filesystem:

```bash
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

Verify:

```bash
df -h
```

---

# 14. Remove OpenSearch Read-Only Mode

```bash
curl -k -u admin:'<password>' \
-X PUT "https://localhost:9200/_all/_settings" \
-H "Content-Type: application/json" \
-d '{"index.blocks.read_only_allow_delete": null}'
```

Restart services:

```bash
systemctl restart wazuh-indexer
systemctl restart wazuh-manager
systemctl restart wazuh-dashboard
```

---

# Outcome

The deployment successfully demonstrated:

- Centralized security monitoring
- Endpoint visibility
- Threat hunting
- SSH authentication monitoring
- MITRE ATT&CK mapping
- Vulnerability assessment
- CIS benchmark auditing
- PCI DSS compliance monitoring
