# Network Traffic Analysis — Wireshark PCAP Investigation

## Overview

This project uses Wireshark to investigate a packet capture associated with an **ET MALWARE Lumma Stealer Victim Fingerprinting Activity** alert.

The objective is to identify the Windows client and associated user, then document packet evidence so incident responders can locate the computer and account.

## Environment

| Component | Value |
|---|---|
| LAN subnet | `10.1.21.0/24` |
| Domain | `win11office[.]com` |
| AD environment name | `WIN11OFFICE` |
| Domain controller IP | `10.1.21.2` |
| Domain controller hostname | `WIN-LU4L24X3UB7` |
| LAN gateway | `10.1.21.1` |
| Broadcast address | `10.1.21.255` |

## Background

During a review of the previous week's alerts, a SOC analyst identifies a signature hit for **ET MALWARE Lumma Stealer Victim Fingerprinting Activity**.

The alert relates to traffic from `153.92.1.49` over **TCP port 80**, occurring on **27 January 2026 at 23:05 UTC**.

A packet capture of the associated internal client's traffic is provided for investigation.

## Investigation Questions

1. What is the IP address of the infected Windows client?
2. What is the MAC address of the client?
3. What is the client's hostname?
4. What is the associated user account name?
5. What is the user's full name?
6. Which domain associated with `153.92.1.49` triggered the alert?

## Tools and Evidence

- **Wireshark:** Packet filtering, protocol inspection, and stream analysis.
- **PCAP:** `traffic-analysis-exercise.pcap`
- **Screenshots:** Relevant packet fields supporting each finding.

## Investigation Approach

1. Locate the connection associated with the alert.
2. Identify the internal client IP and HTTP domain.
3. Correlate the client IP with its MAC address.
4. Identify the hostname and user account from available traffic.
5. Find evidence linking the account to the user's full name.
6. Document findings with packet numbers, UTC timestamps, and screenshots.

## Scope

This is a training exercise. The supplied alert provides the starting hypothesis; conclusions are based on the available packet evidence.



## Investigation Method and Findings

### 1. What is the IP address of the infected Windows client?

I filtered traffic involving the alerted external IP over TCP port 80.

```wireshark
ip.addr == 153.92.1.49 and tcp.port == 80
```

I reviewed the source and destination addresses around the supplied alert time: **27 January 2026 at 23:05 UTC**.

**Finding:** The internal client communicating with the alerted server was `10.1.21.58`.

![Client communication with the alerted server](Images/01-alert-client-ip.png)



### 2. What is the MAC address of the client?

I selected an outbound packet from `10.1.21.58` and inspected the Source address under **Ethernet II**.

```wireshark
ip.src == 10.1.21.58 && ip.dst == 153.92.1.49 && tcp.port == 80
```

The source MAC address was `00:21:5d:c8:0e:f2`.

I then used ARP traffic to confirm the association:

```wireshark
arp.src.proto_ipv4 == 10.1.21.58
```

The ARP response showed:

`10.1.21.58 is at 00:21:5d:c8:0e:f2`

**Answer:** The client MAC address is **`00:21:5d:c8:0e:f2`**.

![ARP confirmation of the client IP and MAC address](Images/03-client-mac-confirmation.png)



