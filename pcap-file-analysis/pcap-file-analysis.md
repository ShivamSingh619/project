# Network Traffic Analysis — Wireshark PCAP Investigation

## Overview

This SOC L1 lab investigates a traffic-analysis PCAP using Wireshark. The investigation focuses on identifying suspicious network activity, correlating related packets, and documenting evidence for a simulated L2 escalation.

## Objective

Determine whether an internal host shows suspicious communication by reviewing DNS, HTTP, TLS, and internal network activity.

The investigation aims to:

- Identify the host generating the activity.
- Establish which destinations it contacted.
- Examine visible requests and responses.
- Build an evidence-based timeline.
- Document network indicators, limitations, and recommended follow-up.

## Investigation Questions

1. Which internal host requires investigation?
2. Which domains and IP addresses did it contact?
3. What requests or data transfers are visible?
4. Are repeated connections or unusual internal activity present?
5. What evidence supports escalation?

## Tools and Evidence

| Item | Purpose |
|---|---|
| Wireshark | Packet filtering, protocol analysis, statistics, and stream review |
| Original PCAP | Primary investigation evidence |
| Screenshots | Preserve relevant packet details and statistics |
| Investigation notes | Record observations, reasoning, and limitations |

## 1. Preserve the Capture

Keep the original PCAP unchanged and analyse a working copy.

Calculate its SHA-256 hash using PowerShell:

```powershell
Get-FileHash .\2026-01-31-traffic-analysis-exercise.pcap -Algorithm SHA256
```

| Detail | Recorded value |
|---|---|
| Filename | `2026-01-31-traffic-analysis-exercise.pcap` |
| SHA-256 | `[Add hash]` |
| Investigation date | `[Add date]` |

![PCAP hash](Images/pcap-file-hash.png)

## 2. Establish the Traffic Overview

In Wireshark:

1. Open the capture.
2. Set the time display to UTC date and time.
3. Review capture details under Statistics → Capture File Properties.
4. Open Statistics → Protocol Hierarchy.
5. Open Statistics → Conversations → IPv4 and sort by Bytes.

Record the packet timestamps rather than assuming the filename represents the capture date.

| Detail | Finding |
|---|---|
| Capture start — UTC | `[Add]` |
| Capture end — UTC | `[Add]` |
| Packet count | `[Add]` |
| Main protocols | `[Add]` |
| Largest conversations | `[Add source, destination, and bytes]` |

### Initial Assessment

`[Describe the traffic mix and explain which conversations need closer review. Traffic volume alone does not prove malicious activity.]`

![Protocol hierarchy](Images/pcap-protocol-hierarchy.png)

![IPv4 conversations](Images/pcap-ip-conversations.png)

## 3. Identify the Host Under Investigation

Start with the internal host identified during the initial capture review:

```wireshark
ip.addr == 10.1.21.58
```

Review Ethernet details and hostname evidence where available:

```wireshark
dhcp || bootp || nbns || llmnr
```

| Detail | Finding |
|---|---|
| Internal IP | `10.1.21.58` |
| MAC address | `[Add if established]` |
| Hostname | `[Add if established]` |
| Supporting packet numbers | `[Add]` |

Do not assign a hostname or user unless packets support that association.

![Host identification evidence](Images/pcap-host-identification.png)

## 4. Investigate DNS Activity

Filter the host's DNS traffic:

```wireshark
ip.addr == 10.1.21.58 && dns
```

Review queries, responses, resolved addresses, and timestamps.

Investigate this domain from the preliminary review:

```wireshark
dns.qry.name == "whitepepper.su"
```

| Domain | Resolved IP | Query time — UTC | Packet number |
|---|---|---|---|
| `[Add]` | `[Add]` | `[Add]` | `[Add]` |

### Assessment

`[Explain which queries warrant review and why. A DNS query proves a lookup, not necessarily a successful connection or malicious activity.]`

![DNS queries and responses](Images/pcap-dns-analysis.png)

## 5. Investigate HTTP Requests and Responses

Display HTTP requests from the host:

```wireshark
ip.src == 10.1.21.58 && http.request
```

Review activity involving the domain:

```wireshark
http.host == "whitepepper.su"
```

Review the observed destination:

```wireshark
ip.addr == 10.1.21.58 && ip.addr == 153.92.1.49
```

For a relevant HTTP packet, select:

**Right-click → Follow → TCP Stream**

Examine:

- Request method and Host header.
- Requested path and parameters.
- User-Agent.
- Response status and content.
- POST body where visible.

| Time — UTC | Source → destination | Method/path | Response or observation | Packet/stream |
|---|---|---|---|---|
| `[Add]` | `[Add]` | `[Add]` | `[Add]` | `[Add]` |

### Assessment

`[Explain the observed /api/set_agent requests and responses without assuming their purpose from the path alone.]`

Redact tokens and sensitive request parameters from public screenshots.

![HTTP request details](Images/pcap-http-requests.png)

![Relevant TCP stream](Images/pcap-http-stream.png)

## 6. Review TLS and External Connections

Filter TLS traffic associated with the host:

```wireshark
ip.addr == 10.1.21.58 && tls
```

Inspect ClientHello messages for a server name where present:

```wireshark
ip.addr == 10.1.21.58 && tls.handshake.type == 1
```

Use Statistics → Conversations → TCP to examine connection duration and bytes in each direction.

| Destination | Port | Server name, if visible | Duration/bytes | Observation |
|---|---|---|---|---|
| `[Add]` | `[Add]` | `[Add]` | `[Add]` | `[Add]` |

### Assessment

`[Document unusual destinations or connection patterns. Encrypted application content cannot be inferred from connection size alone.]`

![TLS connection evidence](Images/pcap-tls-analysis.png)

## 7. Review Internal SMB Activity

Filter SMB-related traffic:

```wireshark
ip.addr == 10.1.21.58 && tcp.port == 445
```

Review decoded SMB/SMB2 messages for server addresses, shares, file operations, and authentication results where visible.

| Observation | Evidence |
|---|---|
| Internal destination | `[Add]` |
| Share/file activity | `[Add if visible]` |
| Authentication result | `[Add if visible]` |
| Relevant packets | `[Add]` |

### Assessment

`[Explain whether the observed activity warrants follow-up. SMB traffic alone does not establish lateral movement.]`

![Internal SMB activity](Images/pcap-smb-analysis.png)

## 8. Examine Repeated Communication

Choose a destination that warrants closer review.

Filter that conversation, then use packet timestamps or Statistics → I/O Graphs to inspect its timing.

Record:

- Frequency and spacing of connections or requests.
- Whether intervals are regular.
- Request/response sizes.
- Whether activity continues throughout the capture.

### Assessment

`[Describe the pattern and alternative explanations, such as normal polling. Repetition alone does not confirm command-and-control.]`

![Communication timing](Images/pcap-communication-pattern.png)

## 9. Correlate the Investigation Timeline

Connect related DNS responses, connections, requests, and subsequent activity.

| Time — UTC | Event | Supporting packet/stream | Interpretation |
|---|---|---|---|
| `[Add]` | DNS lookup | `[Add]` | `[Add]` |
| `[Add]` | Connection to resolved address | `[Add]` | `[Add]` |
| `[Add]` | HTTP request/response | `[Add]` | `[Add]` |
| `[Add]` | Subsequent activity | `[Add]` | `[Add]` |

Keep observed events separate from inferred explanations.

## 10. Document Network Indicators

| Type | Indicator | Evidence and status |
|---|---|---|
| Internal host | `10.1.21.58` | Host under investigation |
| Domain | `whitepepper[.]su` | Investigate using DNS/HTTP evidence |
| External IP | `153.92.1.49` | Observed HTTP destination |
| Request path | `/api/set_agent` | Observed request path requiring interpretation |

Add or revise indicators based on your findings. Do not label every contacted address as malicious.

If reputation checks are performed, record the service, check date, report date, and actual result separately.

## 11. MITRE ATT&CK Assessment

Map only behaviour supported by the investigation.

| Observed behaviour | Technique, if justified | Evidence | Confidence |
|---|---|---|---|
| `[Add]` | `[Add technique and ID]` | `[Add]` | `[Add]` |

Do not map ordinary HTTP to command-and-control, or ordinary SMB to lateral movement, without additional supporting evidence.

If the capture does not justify a technique, document that no confident mapping was made.

## 12. Final Assessment

**Verdict:** `[Suspicious / malicious with supporting evidence / benign / inconclusive]`

**Confidence:** `[Low / medium / high, with explanation]`

### Supporting Findings

- `[Finding with packet reference]`
- `[Finding with packet reference]`
- `[Finding with packet reference]`

### Limitations

- The capture covers a limited observation period.
- Encrypted content may not be visible.
- Network packets do not independently identify the process responsible.
- Endpoint and account logs were not available unless separately supplied.

## 13. Simulated SOC L2 Escalation

**Title:** `[Describe the concrete suspicious activity]`

**Host:** `[IP and verified hostname, if available]`

**Reason for escalation:** `[Explain what requires further investigation]`

**Evidence:** Timeline, packet numbers, filters, screenshots, and indicators.

**Requested follow-up:**

- Correlate network activity with endpoint processes.
- Validate the destinations and business purpose.
- Review related DNS, proxy, firewall, and endpoint logs.
- Assess containment according to the response playbook.

**Status:** Simulated escalation; no actual containment or L2 handoff performed.

If your findings do not warrant escalation, explain that decision instead.

## 14. Skills Practised

- Wireshark display filtering and statistics
- DNS and HTTP analysis
- TCP stream review
- TLS metadata and SMB inspection
- Network indicator extraction
- Timeline reconstruction
- Evidence-based assessment and escalation documentation
