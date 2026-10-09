# Incident Response Investigation: Brute-Force Attack & Cerber Ransomware

## Overview

This project documents an incident response investigation conducted in a Splunk lab environment, supplemented by open-source intelligence (OSINT) research. The investigation began with a user-reported alert concerning unusual request volume against a public-facing website and expanded into a multi-stage incident involving suspected credential compromise, unauthorized web content uploads, and a Cerber ransomware infection affecting an internal file server.

The objective was to correlate available log evidence, establish an incident timeline, identify indicators of compromise (IOCs), assess the scope and impact, and recommend containment, eradication, and recovery measures.

**Key findings**

* Brute-force activity targeting a public-facing web application.
* Successful administrative authentication following repeated login attempts.
* Unauthorized uploads of suspicious web content.
* Evidence of a subsequent internal ransomware infection involving a USB device and a Visual Basic Script (VBS).
* 257 unique files identified as encrypted in the investigation.
* Multiple affected systems, including a public-facing web server, a workstation, and an internal file server.

**Tools:** Splunk, OSINT
**Framework:** MITRE ATT&CK
**Focus areas:** Alert triage, log analysis, incident timeline reconstruction, threat intelligence, IOC identification, and incident response recommendations.

## Initial Alert

| Field                  | Details                               |
| ---------------------- | ------------------------------------- |
| Alert Name             | User-Reported Unusual Request Volume  |
| Alert ID               | `UR-INT-20250603-0047`                |
| Initial Severity       | Medium                                |
| Alert Timestamp        | `2025-06-03 04:51:38 UTC`             |
| Reported Domain        | `imreallynotbatman.com`               |
| Initial Concern        | Repeated requests against the website |
| Investigation Platform | Splunk                                |

The initial alert indicated unusual request activity but did not contain a correlated incident at ingestion. The source IP was also absent from the initially forwarded log artifacts. Further investigation was required to determine whether the activity represented malicious behavior and to establish its scope.

The accompanying [IR Triage Report](reports/IR-Triage-Report.pdf) documents the incident assessment, timeline, impact, and recommended response actions.

## Investigation Methodology

The investigation followed a structured workflow:

1. **Triage:** Reviewed the initial alert and identified the domain and activity requiring investigation.
2. **Log analysis:** Used Splunk to examine authentication events, web activity, host activity, and related security events.
3. **Correlation:** Connected findings across the public-facing web server and internal network systems.
4. **Threat intelligence:** Used OSINT to research associated infrastructure, malware indicators, and obfuscation techniques.
5. **Impact assessment:** Identified affected hosts, suspicious files, and the reported extent of encryption.
6. **Response planning:** Developed recommendations for containment, eradication, recovery, and longer-term security improvements.

---

## Phase 1: Identifying the Attacker and Attack Infrastructure

The investigation identified attacker-related infrastructure and a tool associated with the activity. These findings helped establish the context for subsequent authentication and web-server evidence.

### Attacking Device and Tool

The investigation identified the attacking device IP `40.80.148.42` and the tool Acunetix.

![Attacking device IP](screenshots/Attacking_Device_IP.png)

![Attacker tool evidence](screenshots/Attack_Device_Tool_1.png)

![Attacker tool evidence](screenshots/Attack_Device_Tool_2.png)

![Additional attacker tool evidence](screenshots/Attack_Device_Tool_3.png)

### Associated Infrastructure and Threat Intelligence

The investigation also identified a malicious FQDN associated with the activity and an email address linked to the suspected threat actor.

![Malicious FQDN evidence](screenshots/Malicious_FQDN_1.png)

![Additional malicious FQDN evidence](screenshots/Malicious_FQDN_2.png)

![Additional malicious FQDN evidence](screenshots/Malicious_FQDN_3.png)

![Additional malicious FQDN evidence](screenshots/Malicious_FQDN_4.png)

![Additional malicious FQDN evidence](screenshots/Malicious_FQDN_5.png)

![Threat intelligence email evidence](screenshots/APT_Email_1.png)

![Additional threat intelligence email evidence](screenshots/APT_Email_2.png)

![Additional threat intelligence email evidence](screenshots/APT_Email_3.png)

OSINT research supplemented the log investigation by providing context for suspicious indicators. These findings should be interpreted alongside the lab's available telemetry rather than as independent proof of attribution.

## Phase 2: Brute-Force Activity and Administrative Authentication

Authentication-related events were examined to understand the login activity preceding the web-server compromise.

The investigation identified:

* Repeated password attempts against an administrative account.
* Successful administrative authentication following the brute-force activity.
* 412 unique passwords attempted.
* An average attempted password length of six characters.
* Approximately 92.17 seconds between the analyzed events, as recorded in the lab findings.

These findings support investigating the authentication activity as a potential credential-compromise event. Successful authentication after repeated failures increases concern that the attacker obtained valid credentials.

![First administrative authentication evidence](screenshots/Admin_Auth_1.png)

![Additional administrative authentication evidence](screenshots/Admin_Auth_2.png)

![Average password length](screenshots/Avg_Pwd_Length.png)

![Time between analyzed events](screenshots/Time_Between_Analized_Events.png)

![Unique passwords attempted](screenshots/Unique_Pwd_Count.png)

![Additional unique password count evidence](screenshots/Unique_Pwd_Count_2.png)

Actual passwords and sensitive credential values have intentionally been excluded from this public portfolio.

## Phase 3: Web Application Compromise and Suspicious File Uploads

Further investigation examined the website's content management system and unauthorized file uploads.

The lab investigation identified Joomla as the exploited CMS and found a suspicious image filename:

`poisonivy-is-coming-for-you-batman.jpeg`

The suspicious uploads were relevant to the investigation because they followed the authentication activity and indicated possible unauthorized modification of the public-facing web environment.

![Identified CMS](screenshots/Exploited_CMS.png)

![Suspicious file upload evidence](screenshots/Suspicious_File_Upload_1.png)

![Additional suspicious file upload evidence](screenshots/Suspicious_File_Upload_2.png)

![Additional suspicious file upload evidence](screenshots/Suspicious_File_Upload_3.png)

The investigation treated these uploads as indicators of unauthorized web content activity. The presence of a suspicious filename alone does not establish that a web shell was successfully deployed or executed.

## Phase 4: Malware Indicators and OSINT Research

The investigation examined malware-related artifacts and researched associated infrastructure using OSINT.

### File and Hash Indicators

The lab findings included a suspicious executable, `3791.exe`, with the following recorded hashes:

* **MD5:** `AAE3F5A29935E6ABCC2C2754D12A9AF0`
* **SHA-256:** `9709473ab351387aab9e816eff3910b9f28a7a70202e250ed46dba8f820f34a8`

Hash values can be used to search threat-intelligence sources and compare artifacts across investigations. A hash match should be interpreted in context and does not, by itself, establish that a particular host executed the file.

![Hash evidence](screenshots/MD5_Hash_1.png)

![Additional hash evidence](screenshots/MD5_Hash_2.png)

![Additional hash evidence](screenshots/MD5_Hash_3.png)

### OSINT and Obfuscation

OSINT research identified steganography as an obfuscation technique associated with the lab's malware investigation.

Steganography conceals information within an ordinary-looking carrier, such as an image file. This can complicate analysis when suspicious content is not immediately apparent from the file's appearance.

![OSINT steganography research](screenshots/OSINT_Steg_1.png)

![Additional OSINT research](screenshots/OSINT_Steg_2.png)

![Additional OSINT research](screenshots/OSINT_Steg_3.png)

![Additional OSINT research](screenshots/OSINT_Steg_4.png)

![Additional OSINT research](screenshots/OSINT_Steg_5.png)

## Phase 5: Internal Ransomware Infection

The second stage of the investigation focused on activity within the internal network.

The findings documented:

* A USB device named `MIRANDA_PRI` connected to workstation `192.168.250.100`, identified as `we8105desk`.
* A VBS script associated with the infection.
* A connection to `solidaritedeproximite.org`.
* A suspicious file, `mhtr.jpg`, transferred to the internal file server.
* A ransomware-related temporary file, `121214.tmp`, executed on the file server.
* A subsequent IDS/Suricata alert.

The reported activity occurred on August 24, 2016. The sequence suggested a possible infection path from a workstation to an internal file server, although each causal relationship should be interpreted according to the available lab evidence.

### USB and Script Evidence

![USB evidence](screenshots/USB_Evidence_1.png)

![Additional USB evidence](screenshots/USB_Evidence_2.png)

![Additional USB evidence](screenshots/USB_Evidence_3.png)

![VBS script evidence](screenshots/VBS_Script_1.png)

### Suspicious Network Activity

![First malicious URL evidence](screenshots/Malicious_URL_Visit_1.png)

![Additional malicious URL evidence](screenshots/Malicious_URL_Visit_2.png)

![Ransomware-related FQDN](screenshots/Ransomware_FQDN_1.png)

### File Server and Encryption Impact

The affected internal file server was identified as `192.168.250.20`, or `we9041srv`.

The investigation recorded 257 unique encrypted files. A separate lab finding recorded 401 encrypted files of a particular format; these figures represent different reported measures and should not be added together without further validation.

![File server evidence](screenshots/File_Server_1.png)

![Unique encrypted files](screenshots/Unique_Encrypted_Files_1.png)

![Process associated with suspicious activity](screenshots/Process_ID_1.png)

### Cryptor-Related File Evidence

The investigation also examined the filename `mhtr.jpg`, associated with the suspicious file transferred to the file server.

![Cryptor filename evidence](screenshots/Cryptor_Filename_1.png)

![Additional cryptor filename evidence](screenshots/Cryptor_Filename_2.png)

![Additional cryptor filename evidence](screenshots/Cryptor_Filename_3.png)

![Additional cryptor filename evidence](screenshots/Cryptor_Filename_4.png)

## Phase 6: Incident Timeline and Impact Assessment

The triage report documented the following sequence of events.

| Date          | Time (UTC) | Event                                               |
| ------------- | ---------- | --------------------------------------------------- |
| Aug. 10, 2016 | 21:45:21   | Brute-force activity against the web login          |
| Aug. 10, 2016 | 21:46:33   | Successful administrative authentication            |
| Aug. 10, 2016 | 21:48:05   | Additional successful administrative authentication |
| Aug. 10, 2016 | 21:56:18   | First documented suspicious upload                  |
| Aug. 10, 2016 | 21:58:23   | Additional suspicious upload                        |
| Aug. 10, 2016 | 22:08:13   | Further suspicious upload                           |
| Aug. 10, 2016 | 22:21:58   | Session terminated                                  |
| Aug. 24, 2016 | 16:42:17   | USB device connected to workstation                 |
| Aug. 24, 2016 | 16:43:21   | VBS script associated with the infection introduced |
| Aug. 24, 2016 | 16:48:12   | Connection to a suspicious external domain          |
| Aug. 24, 2016 | 16:48:13   | Suspicious file transferred to the file server      |
| Aug. 24, 2016 | 16:48:21   | Ransomware-related temporary file executed          |
| Aug. 24, 2016 | 16:49:25   | IDS/Suricata alert recorded                         |

The initial alert timestamp is from 2025, while the investigated events are dated 2016. The investigation therefore treats the alert as the supplied investigative starting point and the older timestamps as the historical activity described by the lab artifacts, not as a claim that the attack occurred in 2025.

### Scope and Impact

| Asset or impact          | Finding                                         |
| ------------------------ | ----------------------------------------------- |
| Public-facing web server | `imreallynotbatman.com`                         |
| Internal workstation     | `we8105desk` — `192.168.250.100`                |
| Internal file server     | `we9041srv` — `192.168.250.20`                  |
| Account exposure         | One administrative account reported compromised |
| File impact              | 257 unique files reported encrypted             |
| Additional concern       | Potential credential reuse and lateral movement |

The combination of web-server compromise indicators and later internal ransomware activity warranted a broad investigation of account security, endpoint activity, file access, and network connections.

## MITRE ATT&CK Mapping

The following techniques were documented in the triage report as relevant to the investigated activity.

| Technique                                       | ID        | Investigation relevance                                                                                                 |
| ----------------------------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------- |
| Brute Force                                     | T1110     | Repeated authentication attempts                                                                                        |
| Valid Accounts                                  | T1078     | Successful administrative authentication                                                                                |
| Web Shell                                       | T1505.003 | Potential web-server persistence or unauthorized server access; not independently confirmed by the filename alone       |
| User Execution                                  | T1204     | User or device interaction associated with the infection scenario                                                       |
| Command and Scripting Interpreter: Visual Basic | T1059.005 | VBS script activity                                                                                                     |
| Data Encrypted for Impact                       | T1486     | Reported ransomware encryption                                                                                          |
| Remote Services                                 | T1021     | Lateral-movement technique listed in the triage assessment; requires supporting evidence to confirm the exact mechanism |

Technique mapping provides a framework for organizing observed behaviors. It does not independently prove that every mapped technique occurred.

## Recommended Response and Remediation

### Immediate Containment

* Isolate affected endpoints and servers as appropriate to limit further spread.
* Disable or reset potentially compromised accounts and revoke active sessions.
* Restrict access to the affected web server while investigating unauthorized changes.
* Preserve relevant logs, suspicious files, and other evidence before destructive remediation where feasible.

### Eradication and Recovery

* Identify and remove malicious files, scripts, and unauthorized web content.
* Determine the full scope of affected systems and validate whether additional accounts or assets were compromised.
* Restore encrypted data from verified, clean backups.
* Validate system integrity and monitor restored systems before returning them to normal operation.

### Preventive Controls

* Enforce multifactor authentication, strong password controls, and protections against repeated login attempts.
* Apply timely operating-system, CMS, plugin, and application updates.
* Restrict administrative access and apply least-privilege principles.
* Implement USB/device controls and user security awareness measures.
* Improve network segmentation to limit movement between workstations, servers, and sensitive assets.
* Centralize relevant logs and monitor authentication anomalies, suspicious file activity, and endpoint alerts.
* Review backup isolation, recovery procedures, and restoration testing.

### Detection Improvements

* Correlate authentication failures and successes with web-server activity.
* Monitor privileged account usage and unauthorized file changes.
* Alert on suspicious scripting, unusual external connections, and ransomware indicators.
* Validate that relevant endpoints and network devices forward sufficient telemetry to the SIEM.
* Test detection logic and confirm that generated alerts contain enough context for investigation.

## Key Takeaways

This investigation reinforced several important incident response practices:

* **Validate telemetry before drawing conclusions.** Missing or incomplete log sources can prevent investigators from seeing relevant activity.
* **Correlate events across systems.** Authentication, web-server, endpoint, and network evidence can collectively reveal a broader incident.
* **Use OSINT to add context.** External research can help prioritize and interpret suspicious indicators, but it should not replace internal evidence.
* **Build a defensible timeline.** Event timestamps and relationships help explain the progression of suspected malicious activity.
* **Distinguish findings from assumptions.** Evidence-based reporting should clearly separate confirmed observations, interpretations, and items requiring further validation.
* **Connect technical findings to remediation.** Investigation results are most useful when translated into actionable containment, recovery, and prevention measures.

## Skills Demonstrated

* Splunk-based log analysis and investigation
* Security alert triage
* Authentication and brute-force activity analysis
* Incident timeline reconstruction
* IOC identification and documentation
* OSINT and threat-intelligence research
* MITRE ATT&CK technique mapping
* Ransomware impact assessment
* Incident response and remediation recommendations
* Technical reporting and evidence-based communication

## Evidence and Lab Context

This project was conducted in a lab environment using Splunk as the primary investigative platform and OSINT as a supplementary research source. The screenshots in this repository document the investigation findings and supporting queries or results captured during the lab.

The repository includes the [IR Triage Report](reports/IR-Triage-Report.pdf), which provides the accompanying incident assessment and recommendations.

The findings and timestamps reflect the supplied lab scenario. This project demonstrates investigative analysis and reporting; it should not be interpreted as a live enterprise incident response engagement or as evidence that the analyst deployed production security infrastructure.

---

*Prepared by Zachary Lopater | Cybersecurity Portfolio Project*
