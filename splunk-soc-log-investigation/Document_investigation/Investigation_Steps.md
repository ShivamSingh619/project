## Step 1: Upload the HTTP Log File

1. Open **Splunk** and upload the HTTP log file.
2. Set the time range to **All time**.
3. Verify that the total number of events is **3,990**.

![Splunk HTTP Log Upload](Images/1.png)

## Step 2: Search for Multiple Failed Login Attempts

```spl
# Search the HTTP index for failed/authentication-related login requests
index="http" ("*fail*" OR "*auth*") uri="/login"

# Count matching login requests for each source IP
| stats count BY id.orig_h

# Display only IPs with 20 or more login requests
| where count >= 20
```
![Splunk searching multiple failed login](Images/2.png)

## Step 3: Individually Analyze Each IP for Successful Login Attempts

After identifying IP addresses with multiple login attempts, I investigated each IP individually to determine whether the login attempts were successful or unsuccessful.

### Splunk Query

```spl,
# Search login events for a specific source IP 
index="http" uri="/login" id.orig_h="10.0.0.40"

# Sort events by username and timestamp
| sort 0 username +ts

# Display relevant fields for login analysis
| table ts id.orig_h username auth_result id.resp_h status_code
```

### 1. IP: 10.0.0.40
  The activity from ```10.0.0.40``` shows login attempts associated with the username ```sv_monitor```. The attempts appear to be unsuccessful.

![10.0.0.40 All login fail](Images/3.png)


### 2. IP: 10.0.0.50
The activity from ```10.0.0.50``` appears consistent with normal employee login activity.

![10.0.0.50 look like normal](Images/4.png)


### 3. IP: 10.0.0.81
The activity from ```10.0.0.81``` shows a successful login associated with the username ```sv_backup```.

![10.0.0.81 login confirm](Images/5.png)


### 4. IP: 10.0.0.82
   The activity from ```10.0.0.82``` shows multiple unsuccessful login attempts using different employee usernames.

![10.0.0.82 All login fail](Images/6.png)


### 5. IP: 10.0.0.99
   The activity from ```10.0.0.99``` shows a successful login associated with the username admin.

![10.0.0.99 login successful](Images/7.png)
   
### Summary

I analyzed the five IP addresses individually to identify successful and failed login attempts. `10.0.0.40` (`sv_monitor`) and `10.0.0.82` showed failed login activity, while `10.0.0.81` (`sv_backup`) and `10.0.0.99` (`admin`) showed successful logins. `10.0.0.50` appeared consistent with normal employee activity.

I also investigated the identified IPs for any further related activity but did not find additional relevant events. Based on the available log data, I could not determine any further activity associated with these IPs.



## MITRE ATT&CK Assessment

- **Tactic:** Credential Access
- **Technique:** T1110.001 — Password Guessing
- **Assessment:** Repeated failures against the same account within a short period are consistent with suspected password guessing.
- **Limitation:** The logs do not contain attempted passwords or establish whether the successful login was authorised.


## Simulated SOC L2 Escalation

The identified suspicious IP addresses were reviewed using Splunk to summarize the authentication activity.

The investigation recorded:

- Source and destination IP addresses
- Affected usernames
- First and last event timestamps
- Number of failed authentication attempts
- Number of successful authentication attempts
- Login URI

The summarized Splunk results were prepared as supporting evidence for escalation to the SOC L2 team for further investigation.

![L2 Escalation Evidence](Images/8.png)


