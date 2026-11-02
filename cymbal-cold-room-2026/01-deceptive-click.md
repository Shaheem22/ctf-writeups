<img src="../assets/htb-banner.png" style="max-width: 100%;" align=left />

# Deceptive Click
29<sup>th</sup> October 2026

Prepared by: Muhammad Shaheem (HTB: feanor)

Machine Author(s): Not specified by the event

Difficulty: Very Easy

> **Flag values are redacted.** This writeup covers the reasoning and tools used, not the literal strings submitted. All six questions were solved.

## Scenario
```
Cymbal Investments' SOC receives a high-priority case, "PowerShell Download And Execute", raised by
curated detections on an endpoint in the Denver office. Using Google SecOps (SIEM search, Gemini-assisted
investigations and Google Threat Intelligence), trace the intrusion to its initial point of entry and
uncover the adversary's objectives before critical assets are compromised.
```

## Artifacts Provided

- Google SecOps tenant for Cymbal Investments (Cases, SIEM Search, Entity Explorer)
- Google Threat Intelligence malware profile for the FAKEUPDATES JavaScript dropper

## Initial Analysis

The case's entity list (user, host, process names) gave the first pivots. The malware profile gave indicator names and expected behavior, which I used as search terms but did not trust blindly, because the environment's values were its own.

### Analysis Sub-Section: Scoping the endpoint

The Entity Explorer's search buttons open a pre-filled UDM query for any entity. Searching the compromised host returned endpoint telemetry only (process launches, module loads, risk events). Web activity did not appear under the host search, because proxy logs identify the client by **IP address and user**, not by hostname. Pulling the client IP from an endpoint event unlocked the web logs.

### Analysis Sub-Section: Working backward to initial access

The Gemini case summary named the downloaded archive. Searching for that URL pattern returned the HTTP event, and the event's **referral URL** field named the page that sent the user to it. Time range matters: I scoped the search to the dates on the case's alerts, not the default recent window.

### Analysis Sub-Section: Process injection

Searching injection-style telemetry with the secondary payload as the source process and a SYSTEM process as the target gave the injected instance. The target PID was read directly from the raw event. **The AI-generated case summary quoted a different number than the event**, which is the reason to take every value from the event, not the summary. The expected-format hint (three digits) matched the raw value.

### Analysis Sub-Section: Kerberoasting on the domain controller

Searching the domain controller's Kerberos service-ticket events (Event ID 4769), filtered on the infected host's IP and on **RC4 ticket encryption**, isolated the roasting request. Machine-account service names are noise, and the one tied to a user-owned service account is the target. The full SPN is in the event's service-name field.

### Analysis Sub-Section: Identity change and staging

The event's service SID identified the account that owns the SPN. I confirmed it by finding that account's first logon from the infected host after the ticket request, since the SPN owner and the account used for movement are not guaranteed to match. From there, searching the file server for archive creation (or compression utilities in command lines) by that account located the staging archive.

### ATT&CK techniques

T1189 Drive-by Compromise · T1059.007 / T1059.001 Scripting · T1055.012 Process Hollowing · T1558.003 Kerberoasting · T1078 Valid Accounts · T1560.001 Archive via Utility

## Questions

1. What is the hostname of the compromised endpoint in the Denver office? (expected format: FAKE-HOST-NAME-000.false.name)
   `REDACTED`
2. The GTI Malware Profile states that FAKEUPDATES is delivered via compromised third-party websites injecting malicious JavaScript. By analyzing web proxy logs around the time of the download, what is the domain of the compromised blog that served as the referer for the malicious update archive? (expected format: domainname.com)
   `REDACTED`
3. The secondary payload achieved privilege elevation by hollowing into a SYSTEM process. What was the Target Process ID of the specific svchost.exe instance injected into on the compromised endpoint? (expected format: 000)
   `REDACTED`
4. Following domain reconnaissance, the adversary conducted Kerberoasting against the Domain Controller to harvest service credentials. What is the full Service Principal Name (SPN) requested in the Kerberos TGS ticket? (expected format: XXXXXXX/FAKE-HOST-NAME-00.false.name:0000)
   `REDACTED`
5. Our threat hunting reveals the attacker switched identities using a cracked Kerberos service account. What username was used for subsequent lateral movement? (expected format: username)
   `REDACTED`
6. What is the file name of the compressed zip archive created by the attacker on the file server to stage sensitive financial audit files? (expected format: File_Name.extension)
   `REDACTED`
