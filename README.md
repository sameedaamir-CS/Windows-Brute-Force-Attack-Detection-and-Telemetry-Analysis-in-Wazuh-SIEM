# Windows Brute-Force Attack Detection and Telemetry Analysis in Wazuh SIEM

A home SOC lab project that simulates a Windows brute-force attack and analyzes how Wazuh ingests and classifies the resulting authentication failures (Event ID 4625) with its default ruleset.

## Objective

Verify log ingestion and baseline detection behavior for Windows logon failures in Wazuh before any custom rule tuning.

## Lab Environment

| Component | Details |
|---|---|
| SIEM / XDR | Wazuh Manager and Dashboard |
| Target endpoint | Windows host with Wazuh agent |
| Attack simulation | Automated brute-force / password-spraying attempts |
| Log source | Windows Security Event Log (Event ID 4625) |

## Methodology

1. **Endpoint onboarding:** Installed the Wazuh agent on the Windows host and confirmed the connection to the Wazuh Manager. Verified that the Security event channel is being monitored.
2. **Attack simulation:** Ran automated login attempts against the Windows machine to generate repeated authentication failures.
3. **Telemetry analysis:** Reviewed incoming events in the Wazuh Dashboard (Security Events and Discover) and inspected the raw log fields.
4. **Rule investigation:** Traced how Wazuh maps these events to its default rules.

## Findings

**Log ingestion:** Windows Event ID 4625 (failed logon) was successfully ingested and visible in the dashboard.

**Key fields**
- Failure reason: Unknown user name or bad password
- Sub-status: `0xC000006A` (valid account, incorrect password)

**Default rule match**
- Rule ID: `60122`
- Level: 5
- Rule file: `0580-win-security_rules.xml`
- Event IDs covered: 4625 / 529

## MITRE ATT&CK Mapping

- T1110: Brute Force
- T1110.003: Password Spraying

## Key Takeaways

- Wazuh rates individual logon failures at a low level (5) by default, which avoids alert fatigue from ordinary typos. Detection value comes from frequency and source correlation, not single events.
- Sub-status codes such as `0xC000006A` help distinguish a wrong password for a valid account from attempts against non-existent users.
- Confirming ingestion and baseline rule behavior is the necessary first step before building custom detection logic.

## Screenshots

<!-- Add your screenshots to a /screenshots folder and update the paths below -->
![Raw Event ID 4625 log](screenshots/event-4625-raw.png)
![Rule 60122 alert in Wazuh Dashboard](screenshots/rule-60122-alert.png)

## Future Work

- Write a custom threshold-based correlation rule that escalates repeated failures from the same source IP within a time window.
- Test detection of successful logon (Event ID 4624) following a series of failures.

## Disclaimer

All testing was performed in an isolated lab environment on systems I own.
