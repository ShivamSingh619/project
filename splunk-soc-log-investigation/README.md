# Splunk SOC Log Investigation

A simulated SOC investigation project focused on analyzing authentication logs in Splunk to identify suspicious login activity.

The investigation examines repeated failed login attempts followed by successful authentication, reconstructs event timelines, correlates activity across source IPs and user accounts, and prepares a simulated escalation to SOC L2.

## Objective

Identify suspicious authentication activity, reconstruct event timelines, document findings, and prepare a simulated escalation to SOC L2.

## Tools, Technologies & Dataset

- Splunk
- Splunk Search Processing Language (SPL)
- MITRE ATT&CK
- Simulated dataset containing 3,990 HTTP log file.


## Investigation Workflow

1. Upload the log file into splunk.
2. Search for repeated authentication failures.
3. Investigate suspicious source IPs.
4. Correlate failed and successful logins.
5. Review related account activity.
6. Reconstruct the event timeline.
7. Assess the activity and possible legitimate explanations.
8. Map relevant behaviour to MITRE ATT&CK.
9. Write a alert report and prepare a simulated L2 escalation.


## MITRE ATT&CK Assessment

- **Tactic:** Credential Access
- **Technique:** T1110.001 — Password Guessing
- **Assessment:** Repeated failures against the same account within a short period are consistent with suspected password guessing.
- **Limitation:** The logs do not contain attempted passwords or establish whether the successful login was authorised.

## Simulated L2 Escalation

The escalation documents the affected accounts, source and destination IPs, timestamps, authentication counts, assessment, and supporting evidence.

 ### Recommended follow-up:

- Validate the source hosts.
- Confirm whether the logins were expected.
- Review subsequent account and session activity.
- Assess containment according to the organisation's response procedures.

## Skills Practised

- Splunk SPL searches
- Authentication log analysis
- Event correlation and timeline reconstruction
- Evidence-based assessment
- MITRE ATT&CK mapping
- Investigation documentation and escalation preparation
