# Splunk SIEM — Windows Security Monitoring

**Project 2 | Hands-on SOC Monitoring Lab**

## 1. Project Overview

This project demonstrates the use of Splunk Enterprise as a SIEM solution in a Windows Server environment.

A Windows Server 2022 Domain Controller generated Windows Security Events. Splunk Universal Forwarder collected the Security logs and forwarded them to Splunk Enterprise for monitoring, searching, and investigation.

The lab focused on authentication monitoring, Windows Event ID analysis, source-address investigation, privileged activity, and SOC dashboard creation.

## 2. Lab Architecture

| Component | Purpose |
|---|---|
| Windows Server 2022 (DC01 / NKAD) | Windows Security Event Log source |
| Splunk Universal Forwarder | Log collection and forwarding |
| Splunk Enterprise | SIEM, search, analysis, and dashboard |
| VMware Workstation | Virtual lab environment |
| TCP 9997 | Splunk receiving port |

**Log flow:**

Windows Server 2022 → Splunk Universal Forwarder → Splunk Enterprise

## 3. Log Collection Configuration

The Universal Forwarder was configured to collect the Windows Security Event Log.

```ini
[WinEventLog://Security]
disabled = 0
index = main
start_from = oldest
current_only = 0
checkpointInterval = 5
```

Windows Security events were successfully received in Splunk under the `main` index.

## 4. Windows Security Event Investigation

### Event ID 4624 — Successful Logons

Used to identify successful authentication activity.

```spl
index=main host=NKAD EventCode=4624
```

### Event ID 4625 — Failed Logons

Used to investigate failed authentication attempts.

```spl
index=main host=NKAD sourcetype="WinEventLog:Security" EventCode=4625
```

**Investigation findings:**

- Account: Shadow
- Source IP: 127.0.0.1
- Logon Type: 2
- Failure Reason: Unknown user name or bad password

This was a controlled failed-login test generated in the lab, not a confirmed remote attack.

### Event ID 4672 — Special Privileges Assigned

Used to investigate privileged activity associated with logon sessions.

```spl
index=main host=NKAD sourcetype="WinEventLog:Security" EventCode=4672
| stats count by Account_Name
| sort - count
```

**Observed accounts included:**
- SYSTEM
- Shadow

Event ID 4672 was treated as an investigation point, not automatically classified as malicious.

## 5. Additional Windows Security Events

Other event types observed during the lab included:

| Event ID | Activity |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4634 | Logoff |
| 4648 | Explicit credential use |
| 4672 | Special privileges assigned |

## 6. Splunk SOC Monitoring Dashboard

Created a dashboard named **Splunk SOC Monitoring**.

The dashboard contains four monitoring panels:

1. Failed Logons by Account and Source IP
2. Authentication Success vs Failure
3. Privileged Account Activity
4. Windows Security Events by EventCode

The dashboard provides a centralized view of the Windows Security activity investigated during the project.

## 7. Skills Practiced

- Splunk Enterprise fundamentals
- SPL searching and filtering
- Windows Security Event Log analysis
- Authentication monitoring
- Failed-logon investigation
- Source IP investigation
- Privileged activity monitoring
- Security event analysis
- SOC dashboard creation
- Basic SOC investigation workflow

## 8. Evidence & Documentation

The complete technical evidence report includes screenshots of the lab configuration, log ingestion, SPL investigations, Windows Security events, and the completed Splunk dashboard.

📄 [View Project 2 — Splunk SIEM Evidence Report](Project_2_Splunk_SIEM_Evidence_Report.pdf)

---

**Environment:** Windows Server 2022 | Splunk Enterprise | Splunk Universal Forwarder | VMware Workstation

*Hands-on SIEM monitoring and Windows Security Event investigation.*
