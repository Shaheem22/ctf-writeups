<img src="../assets/htb-banner.png" style="max-width: 100%;" align=left />

# OAuth Heist
1<sup>st</sup> November 2026

Prepared by: Muhammad Shaheem (HTB: feanor)

Machine Author(s): Not specified by the event

Difficulty: Easy

> **Flag values are redacted.** This writeup covers the reasoning and tools used, not the literal strings submitted. All six questions were solved.

## Scenario
```
A threat actor takes over an employee's account through social engineering, pivots through cloud
applications, finds credentials in an internal document, reaches an internal database server and
exfiltrates data. Seed material: a Google Threat Intelligence report on ShinyHunters-branded cloud
application extortion. The Emerging Threats campaign built from that report points to the compromised
employee and a malicious OAuth application.
```

## Artifacts Provided

- Google SecOps tenant for Cymbal Investments (Emerging Threats, SIEM Search, Entity Explorer)
- Google Threat Intelligence report on cloud-application extortion by ShinyHunters-branded actors
- A scenario hint about which Emerging Threats campaign to open

## Initial Analysis

### Analysis Sub-Section: Starting from the campaign

Searching Emerging Threats for the actor's name returned several single-product reports. The right one covered **multiple cloud platforms and had both of its rules enabled**. Its rule detections and indicators named the employee, the malicious application and the first detection time. I recorded those as the first entries in my notes.

### Analysis Sub-Section: Widening the time range

Searching the employee's web activity under the default range returned nothing. Setting the range to cover the full incident window (about a week) immediately produced results. If a pivot is empty, widen the time range before changing the query.

### Analysis Sub-Section: Phishing and the account takeover

Searching the employee's workstation for HTTP **POST** events before the first suspicious sign-in found the credential submission to a lookalike single-sign-on page. Sign-in and factor-enrollment events then separated the attacker from the account owner using the source IP's context (anonymizing proxy, hosting ASN), device details and a device fingerprint that the owner had never used.

### Analysis Sub-Section: OAuth application and mailbox cleanup

The token-authorize audit event named the application, its client ID and its **mail-modifying scopes**. Searching mailbox audit events tied to that client ID listed the messages the application handled and their subjects. Two notification messages were handled within a couple of minutes of each other, and the question asked for the first. I compared the audit events' own timestamps, not delivery times from a different log source. The two sources' times did not line up, so the order within the audit log was what mattered. The application did not operate from the attacker's sign-in IP, so searching only that IP missed its actions.

### Analysis Sub-Section: Document access and the pivot to the database

Cloud file-access events from the attacker's proxy infrastructure showed the sensitive document. A logon from the employee's workstation to the database server, using a service account that is not the employee, followed. The Entity Explorer view of that logon event listed the account, the server and the workstation together.

### Analysis Sub-Section: Dump, archive and exfiltration

Process-launch events on the database host, tied to a remote-management parent process, showed a database client producing a backup, then a compression utility packing it. The compression command line supplied the exact name of the archive. Searching **raw logs** for that archive name returned the HTTP upload event, whose URL field contained the full destination.

### ATT&CK techniques

T1566.002 Phishing Link · T1098.005 Device Registration · T1528 Steal Application Access Token · T1070.008 Clear Mailbox Data · T1552.001 Credentials in Files · T1021.006 Windows Remote Management · T1560.001 Archive via Utility · T1567 Exfiltration Over Web Service

## Questions

1. What is the phishing domain where employee credentials were submitted via an HTTP POST request? (expected format: domainname.com)
   `REDACTED`
2. To prevent the compromised employee from noticing the persistence mechanism, the adversary abused a newly authorized third-party OAuth application to permanently purge security notifications from the victim's inbox. What is the exact subject line of the first permanently deleted email, which alerted on multi-factor authentication changes? (expected format: Hello! This is a fake email subject line)
   `REDACTED`
3. Continuing to monitor the compromised employee's cloud activity from the attacker's proxy infrastructure, what sensitive internal document did the adversary locate and download? (expected format: File_Name.extension)
   `REDACTED`
4. Armed with credentials discovered in the stolen document, what privileged service account did the attacker authenticate to from the compromised employee's workstation to access the internal database server? (expected format: username)
   `REDACTED`
5. Once authenticated to that internal server, what is the file name of the raw database dump exported by the attacker prior to compressing it into an archive? (expected format: file_name.extension)
   `REDACTED`
6. What is the full exfiltration URL (including destination IP and port) used by the attacker to transfer the compressed archive? (expected format: http://8.8.8.8:0000/path/file_name.extensions)
   `REDACTED`
