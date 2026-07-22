# MySQL Database Ransomware Incident Report

## Executive Summary

On **July 19, 2026**, a threat actor successfully compromised the MySQL server **corp-db01-int01** by remotely authenticating as the **root** user from the external IP address **64.89.163.178**. Within approximately two minutes, the attacker enumerated the database schema, removed foreign key constraints, deleted multiple database tables, deployed a ransomware note, attempted to erase forensic evidence, revoked administrative privileges, and shut down the MySQL service.

Analysis of MySQL audit logs, Microsoft Defender XDR telemetry, and Microsoft Sentinel data confirmed that the attack followed a scripted sequence consistent with automated MySQL ransomware campaigns targeting Internet-exposed database servers. No evidence of lateral movement or additional endpoint compromise was identified during the investigation timeframe.

---

# Incident Overview

| Item | Value |
|------|-------|
| Incident Type | MySQL Database Ransomware |
| Severity | Critical |
| Affected Host | corp-db01-int01 |
| Operating System | Windows 11 |
| Database | MySQL |
| Initial Access | Remote MySQL Authentication |
| Duration | ~2 Minutes |
| Malicious IP | 64.89.163.178 |

---

# Indicators of Compromise

## External IP

| Indicator | Value |
|----------|-------|
| IP Address | 64.89.163.178 |
| Organization | Meowcore Softworks LLC |
| Country | Germany |
| City | Frankfurt am Main |

## Malicious SQL Artifacts

- RECOVER_YOUR_DATA
- RESET MASTER
- PURGE BINARY LOGS
- DROP TABLE
- REVOKE
- SHUTDOWN

---

# Timeline of Events

| Time (UTC) | Event | Description |
|------------|-------|-------------|
| 2026-07-18 14:58:01 | Failed Authentication | Failed root login observed from 64.89.163.148 |
| 2026-07-19 02:10:12 | Network Connection | External connection established to TCP/3306 |
| 2026-07-19 02:10:29 | Initial Access | Successful authentication as root |
| 2026-07-19 02:10:30 | Session Initialization | SQL mode modified |
| 2026-07-19 02:10:36 | Database Discovery | INFORMATION_SCHEMA enumeration |
| 2026-07-19 02:10:41 | Foreign Key Removal | Constraints removed |
| 2026-07-19 02:10:42 | Data Destruction | Multiple tables deleted |
| 2026-07-19 02:10:42 | Ransom Deployment | RECOVER_YOUR_DATA created |
| 2026-07-19 02:12:06 | Recovery Inhibition | RESET MASTER executed |
| 2026-07-19 02:12:06 | Recovery Inhibition | PURGE BINARY LOGS executed |
| 2026-07-19 02:12:07 | Privilege Manipulation | Root privileges revoked |
| 2026-07-19 02:12:07 | Service Impact | MySQL shutdown |

---

# Investigation Findings

## Initial Access

The attacker authenticated remotely to the MySQL service using the privileged **root** account over TCP port 3306.

## Database Discovery

Following authentication, the attacker queried MySQL metadata tables to enumerate schemas, tablespaces, partitions, and storage information. This reconnaissance enabled identification of high-value database objects prior to destructive actions.

## Data Destruction

Foreign key constraints were removed from multiple tables before `DROP TABLE` commands were executed. This allowed the attacker to bypass referential integrity protections and rapidly delete application data.

## Ransomware Deployment

The attacker created a table named **RECOVER_YOUR_DATA** containing a Bitcoin payment demand. This behavior is consistent with automated MySQL ransomware campaigns targeting exposed database servers.

## Recovery Inhibition

To reduce the likelihood of successful recovery, the attacker executed `RESET MASTER` and `PURGE BINARY LOGS`, removing transaction history used for point-in-time recovery and forensic reconstruction.

## Privilege Manipulation

Administrative privileges were revoked from the remote root account, limiting the ability of administrators to immediately restore or modify the compromised database.

## Service Shutdown

The attack concluded with execution of the MySQL `SHUTDOWN` command, rendering the database unavailable.

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ID | Evidence |
|--------|-----------|----|----------|
| Initial Access | External Remote Services | T1133 | Remote MySQL connection |
| Persistence | Valid Accounts | T1078 | Root authentication |
| Discovery | Database Discovery | T1046 | INFORMATION_SCHEMA enumeration |
| Impact | Data Destruction | T1485 | DROP TABLE operations |
| Impact | Inhibit System Recovery | T1490 | RESET MASTER, PURGE BINARY LOGS |
| Persistence | Account Manipulation | T1098 | REVOKE privileges |

---

# Assessment

The investigation confirms a successful automated ransomware attack against an Internet-exposed MySQL server. The attacker gained privileged access, enumerated the database, destroyed multiple tables, deployed a ransom note, inhibited recovery by deleting binary logs, modified administrative privileges, and shut down the database service. The speed and sequence of observed commands strongly indicate execution by an automated ransomware script rather than manual interaction.

---

# Recommendations

1. Remove MySQL from public Internet exposure.
2. Restrict administrative access using firewall allowlists or VPN connectivity.
3. Disable remote root authentication.
4. Enable centralized logging with long-term retention.
5. Maintain immutable or offline backups.
6. Monitor for destructive SQL statements including `DROP TABLE`, `RESET MASTER`, `PURGE BINARY LOGS`, `REVOKE`, and `SHUTDOWN`.
7. Implement Microsoft Sentinel analytics rules for privileged database activity.
8. Regularly review database user permissions and authentication logs.

---

# Conclusion

This investigation demonstrates the effectiveness of combining Microsoft Sentinel, Microsoft Defender XDR, and MySQL audit logs to reconstruct a complete attack lifecycle. By correlating endpoint, network, and database telemetry, the attack was traced from initial access through impact, enabling accurate documentation of attacker behavior and identification of opportunities to strengthen detection and defensive controls.
