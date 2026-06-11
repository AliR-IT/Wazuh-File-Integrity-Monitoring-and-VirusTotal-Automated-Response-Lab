# Wazuh File Integrity Monitoring and VirusTotal Automated Response Lab

## TL;DR

Built a Wazuh-based security monitoring lab using Ubuntu systems to implement real-time File Integrity Monitoring (FIM), integrate VirusTotal reputation checks, and automate malware remediation through Active Response. The project demonstrated how endpoint telemetry can trigger automated defensive actions after a malicious file is detected.

---

# Project Objectives

* Configure real-time File Integrity Monitoring (FIM) for a monitored directory.
* Validate that file creation events generate security telemetry.
* Integrate VirusTotal with Wazuh to enrich FIM events with reputation analysis.
* Trigger automated incident response when a known malicious file is detected.
* Observe and investigate alerts generated throughout the detection and response lifecycle.

---

# Lab Architecture

* **Wazuh Manager (Ubuntu):** Centralized log collection, rule evaluation, VirusTotal integration, and Active Response orchestration.
* **Wazuh Agent (Ubuntu):** Monitored endpoint responsible for generating file integrity events.
* **Monitored Directory:** `/tmp/malware/` configured for real-time FIM.
* **VirusTotal Integration:** Used to enrich detected file events with external reputation information.
* **Active Response Script:** Automatically removes confirmed malicious files from the monitored endpoint.

```text
               +----------------------+
               |    Wazuh Manager      |
               |----------------------|
               | FIM Rules            |
               | VirusTotal API       |
               | Active Response      |
               +----------+-----------+
                          |
                    Wazuh Agent
                          |
             /tmp/malware/ Directory
                          |
      File Created -> FIM Event -> VirusTotal Check
                          |
               Malicious Detection Confirmed
                          |
          Active Response Script Executes
                          |
          Malicious File Automatically Removed
```
---

# Environment Setup

The lab consisted of two Ubuntu virtual machines:

* Ubuntu Wazuh Manager
* Ubuntu Wazuh Agent

A dedicated `/tmp/malware/` directory with permissive permissions was configured on the agent for controlled testing. Wazuh Syscheck was configured to monitor the directory in real time.

![Wazuh Syscheck](Screenshots/Syscheck%20configuration.png)

<div align="center">
  *Figure: Real-time monitoring configured for the dedicated test directory.
</div>

---

# Attack Simulation / Telemetry Generation

To validate File Integrity Monitoring, a benign test file was first created within the monitored directory to confirm that telemetry was successfully generated.

After verifying FIM functionality, the industry-standard EICAR anti-malware test file was downloaded into the monitored directory. The EICAR file is intentionally designed for security testing and does not represent a real malware sample.

This activity generated:

* File creation events
* Syscheck alerts
* VirusTotal reputation lookups
* Threat hunting telemetry
* Active Response execution logs

![FIM Alert](Screenshots/File%20Integrity%20Monitoring.png)

<div align="center">
  *Figure: File Integrity Monitoring event generated after file creation in the monitored directory.*
</div>

---

# Detection Engineering / Detection Logic

The project combined multiple detection components:

* Wazuh Syscheck monitored file activity in real time.
* VirusTotal integration enriched file events using reputation data.
* Detections associated with malicious files triggered Wazuh alerting.
* Active Response rules were configured to invoke an automated remediation script whenever the VirusTotal detection rule fired.

Rather than relying solely on analyst intervention, confirmed detections initiated automated containment by removing the malicious file from the endpoint.

---

# Investigation & Findings

Analysis of generated alerts showed:

* Successful detection of monitored file creation events.
* VirusTotal enrichment identifying the EICAR test file as malicious across numerous detection engines.
* Corresponding security alerts generated within Wazuh's Threat Hunting interface.
* Successful execution of the configured Active Response script.
* Confirmation through logs that the malicious test file was automatically removed from the monitored system.

Investigating the full event chain demonstrated how endpoint telemetry, external threat intelligence, and automated response can work together to reduce analyst response time.

![VirusTotal Alert](Screenshots/VirusTotal%20enrichment%20alert.png)

<div align="center">
  *Figure: VirusTotal enrichment alert identifying the EICAR test file.*
</div>


![Active Response Alert](Screenshots/Automatic%20Remediation.png)

<div align="center">
  *Figure: Active Response event confirming automated remediation of the detected file.*
</div>

---

# MITRE ATT&CK Mapping

Although the project focused on defensive validation rather than adversary emulation, the observed behaviors align with:

| Technique | Description                                                                             |
| --------- | --------------------------------------------------------------------------------------- |
| T1204     | User Execution (simulated file acquisition)                                             |
| T1070     | Indicator Removal on Host (automated deletion of malicious artifact during remediation) |

---

# Key Takeaways

* Successfully implemented real-time File Integrity Monitoring using Wazuh Syscheck.
* Validated external threat intelligence enrichment through VirusTotal integration.
* Demonstrated automated remediation using Wazuh Active Response.
* Correlated endpoint telemetry with threat intelligence to support rapid incident response.
* Reinforced the value of combining detection engineering with automated containment workflows.

---

# Tools Used

* Wazuh Manager
* Wazuh Agent
* Ubuntu Linux
* File Integrity Monitoring (Syscheck)
* VirusTotal API
* Active Response

---

# Final Notes

This project was conducted in a controlled lab environment using the EICAR anti-malware test file and official Wazuh guidance. The objective was to validate detection and automated response capabilities without using live malware. The exercise highlights practical SOC Analyst skills in telemetry analysis, alert investigation, threat intelligence integration, and security automation.
