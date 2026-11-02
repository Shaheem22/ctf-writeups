<img src="../assets/htb-banner.png" style="max-width: 100%;" align=left />

# Rogue Synthesis
1<sup>st</sup> November 2026

Prepared by: Muhammad Shaheem (HTB: feanor)

Machine Author(s): Not specified by the event

Difficulty: Medium

> **Flag values are redacted.** This writeup covers the reasoning and tools used, not the literal strings submitted. All six questions were solved.

## Scenario
```
Cymbal Investments' Threat Hunting team receives an intelligence bulletin about AI-enabled threats.
Using Google Threat Intelligence reporting and the Google SecOps SIEM, hunt down the initial compromise
and identify the adversary's ultimate target in the CI/CD pipeline.
```

## Artifacts Provided

- Google SecOps tenant for Cymbal Investments (SIEM Search, Entity Explorer, Emerging Threats)
- Google Threat Intelligence report on how threat actors use AI tools, including AI-enabled malware families
- No scenario hints were provided for this category

## Initial Analysis

### Analysis Sub-Section: Finding a starting point

The Emerging Threats cards that matched the report showed **no enabled rules and no indicator matches**, so there was no campaign to open. Instead I hunted directly from the report: it names the models and API endpoints that the malware families call. Searching SIEM network events for that API traffic found one workstation, one user and a **first-call timestamp**. That timestamp anchored the rest of the investigation.

### Analysis Sub-Section: What came before the API call

Searching the developer's web activity in the minutes before the call (by IP, since the workstation's hostname is not in network events, where `hostname` is the destination) showed a short chain of requests. The last download before the call, from a domain that mimicked a legitimate marketplace, was the entry point.

### Analysis Sub-Section: Staging and the compiled binary

Searching file and process events after the call showed the malware's activity in a single folder: a log file, a source file and a compile step. **The compiled binary did not appear as a file-creation event.** It appeared as a process event for the compiler and later as a module load for the binary itself. The question asked for the executable in the same folder as the log file, so I confirmed the folder from the full paths of both files.

### Analysis Sub-Section: Token harvesting and exfiltration

A process launched under a SYSTEM host process (the injected instance) ran a command line that read a credential store inside the developer's cloud SDK configuration folder. The command line itself named the file. Shortly afterwards, an HTTP POST to a telemetry-style path on an external domain carried the data out. Filtering POSTs after the token read and ignoring well-known domains isolated it.

### Analysis Sub-Section: Pivot into the build pipeline

A network-connection event from the workstation, over a remote-management port and under a service account, led to a build server. On that server, file operations showed the attacker preserving a copy of a pipeline script and then overwriting the original. The question asked for the overwritten script, not the backup copy, so I checked for the overwrite itself. The CI/CD pipeline was the target.

### ATT&CK techniques

T1195.002 Supply Chain Compromise · T1027 Obfuscated Files or Information · T1055.012 Process Hollowing · T1552.001 Credentials in Files · T1567 Exfiltration Over Web Service · T1021.006 Windows Remote Management · T1078 Valid Accounts

## Questions

1. What developer username initiated a request to the model used by our AI threat? (expected format: user.name)
   `REDACTED`
2. What package was downloaded immediately prior to the API query? (expected format: File_Name.extension)
   `REDACTED`
3. Following the outbound API request, what compiled executable was dropped into the local directory used by the malware for staging? (expected format: file_name.extension)
   `REDACTED`
4. After elevating privileges via process hollowing on the developer's workstation, what existing file in the user's local cloud SDK configuration directory was read by the injected process to harvest tokens? (expected format: file_name.extension)
   `REDACTED`
5. Shortly after the local tokens were harvested on the workstation, the stolen data was exfiltrated via an outbound HTTP POST request to an external URI path masquerading as a telemetry endpoint. To what external domain was the data sent? (expected format: domain-name.com)
   `REDACTED`
6. Using the harvested service account to pivot laterally from the developer's workstation to a secondary server, what PowerShell script was overwritten by the attacker via PowerShell to establish persistence? (expected format: file_name.extension)
   `REDACTED`
