## Step 1: Upload the HTTP Log File

1. Open **Splunk** and upload the HTTP log file.
2. Set the time range to **All time**.
3. Verify that the total number of events is **4,000**.

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

## Step 3: Every ip indiviusal serach who is successful login 

index="http" uri="/login" id.orig_h="10.0.0.50"
| sort 0 username +ts
| table ts id.orig_h username auth_result id.resp_h status_code

1. i check thsi ip 10.0.0.40
  this is might be successful login username sv_monitor 

![10.0.0.40 All login fail](Images/3.png)


2. check this ip 10.0.0.50 this
this is look like normal employee's

![10.0.0.50 look like normal](Images/4.png)


3. check this ip 10.0.0.81
this is look like successfully login username is sv_backup

![10.0.0.81 login confirm](Images/5.png)


4. check this ip 10.0.0.82
   this is look like try not successful to login try diff employee id

![10.0.0.82 All login fail](Images/6.png)


5. check this ip 10.0.0.99
   this is might be successful login username admin  

![10.0.0.99 login successful](Images/7.png)
   

