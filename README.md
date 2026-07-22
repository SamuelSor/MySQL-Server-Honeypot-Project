# 🍯MySQL Honeypot & Ransomware Threat Hunt with Microsoft Sentinel

## Project Overview

This project demonstrates the design, deployment, monitoring, and investigation of a cloud-hosted MySQL honeypot that successfully captured a real-world automated ransomware attack.

The lab was built in Microsoft Azure using an Windows 11 virtual machine configured with MySQL, audit logging, Microsoft Defender for Endpoint, Azure Monitor Agent, and Microsoft Sentinel. The goal was to attract opportunistic attacks against an intentionally exposed MySQL service, collect telemetry, and perform a complete incident investigation using SIEM and endpoint telemetry.

During the project, a threat actor successfully authenticated to the exposed MySQL instance, enumerated the database, destroyed multiple tables, deployed a ransomware note, attempted to erase forensic evidence, revoked administrative privileges, and shut down the database. Using Microsoft Sentinel, MySQL audit logs, and Microsoft Defender XDR, the complete attack lifecycle was reconstructed and mapped to the MITRE ATT&CK framework.

---

# Project Objectives

- Deploy an Internet-accessible MySQL honeypot
- Configure centralized logging with Microsoft Sentinel
- Capture real attacker activity
- Perform threat hunting and incident response
- Reconstruct the complete attack timeline
- Identify attacker TTPs using MITRE ATT&CK
- Document findings and provide remediation recommendations

---

# Lab Architecture

```
                    Internet
                         │
                         ▼
               Threat Actor
               64.89.163.178
                         │
                    TCP/3306
                         │
                         ▼
          Azure Windows 11 VM
          ┌────────────────────────┐
          │      MySQL Server      │
          │    MySQL Audit Logs    │
          │ Azure Monitor Agent    │
          └────────────┬───────────┘
                       │
                       ▼
            Log Analytics Workspace
                       │
                       ▼
             Microsoft Sentinel
      ┌────────────────────────────────┐
      │ Analytics Rules                │
      │ Threat Hunting                 │
      │ Incident Investigation         │
      │ MITRE ATT&CK Mapping           │
      └────────────────────────────────┘
```

---

# Technologies Used

## Cloud

- Microsoft Azure
- Azure Virtual Machine
- Azure Monitor
- Log Analytics Workspace

## Security

- Microsoft Sentinel
- Microsoft Defender XDR
- Microsoft Defender for Endpoint

## Operating System

- Windows 11

## Database

- MySQL

## Languages

- Kusto Query Language (KQL)
- SQL

---

# Honeypot Configuration

The honeypot consisted of an Ubuntu Server virtual machine running MySQL with audit logging enabled.

The database was intentionally exposed over TCP port **3306** to simulate a common real-world misconfiguration and attract automated attacks targeting publicly accessible databases.

Telemetry from the operating system, network connections, and MySQL audit logs was forwarded into Microsoft Sentinel for centralized monitoring and investigation.

### Data Sources

- MySQL Audit Logs
- Device Network Events
- Device Process Events
- Device Logon Events
- Microsoft Defender for Endpoint

---

# Investigation Summary

**[Full Incident Report](https://github.com/SamuelSor/MySQL-Server-Honeypot-Project/blob/main/MySQL-Ransomware-Incident-Report.md)

The investigation identified a successful ransomware attack against the exposed MySQL server.

The attacker:

- Authenticated remotely using the MySQL root account
- Enumerated the database schema
- Removed foreign key constraints
- Deleted multiple database tables
- Created a ransomware note
- Reset and purged MySQL binary logs
- Revoked root privileges
- Shut down the MySQL service

The entire attack completed in approximately **two minutes**, indicating the use of an automated ransomware script.

---

# Attack Timeline

| Time (UTC) | Event |
|------------|-------|
| Failed root authentication observed |
| Successful remote root login |
| Database enumeration |
| Foreign key removal |
| Multiple DROP TABLE commands |
| Ransom note deployment |
| Binary log deletion |
| Root privilege revocation |
| Database shutdown |

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|----------|-----------------------------|-----------|
| Initial Access | External Remote Services | T1133 |
| Persistence | Valid Accounts | T1078 |
| Discovery | Database Discovery | T1046 |
| Impact | Data Destruction | T1485 |
| Impact | Inhibit System Recovery | T1490 |
| Persistence | Account Manipulation | T1098 |

---

# Skills Demonstrated

- Microsoft Sentinel
- Microsoft Defender XDR
- Threat Hunting
- Incident Response
- Digital Forensics
- MySQL Administration
- Database Security
- KQL
- SQL
- MITRE ATT&CK
- Log Correlation
- Timeline Reconstruction
- Cloud Security
- Detection Engineering

---

# Lessons Learned

This project demonstrates how quickly exposed database services can be compromised by automated ransomware campaigns. Correlating MySQL audit logs with endpoint and network telemetry enabled reconstruction of the complete attack lifecycle and highlighted the importance of centralized logging, least-privilege administration, and immutable backups. The investigation also reinforced the value of proactive detection engineering for identifying destructive SQL activity before significant impact occurs.

---

# References

- MITRE ATT&CK Framework
- Microsoft Sentinel Documentation
- Microsoft Defender XDR Documentation
- MySQL Audit Log Documentation
