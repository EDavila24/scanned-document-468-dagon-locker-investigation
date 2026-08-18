<h1>SOC CTF</h1>
<h2>Dagon Locker Ransomware Investigation</h2>

This CTF is made by [Anthony Jirouschek](https://www.linkedin.com/in/anthonyjirouschek/) from a Cybersecurity Discord community (https://discord.gg/DCaRgbkWJ) aimed to 'bridge the gap' from certifications to landing a job, based on the [Scanned Document 468 SOC CTF challenge](https://github.com/jirosgyros/soc-ctf-scanned-document-468).

I investigated a simulated ransomware incident using provided security telemetry to identify malicious activity, understood how the intrusion developed, and determined the attacker’s impact on the environment. This challenge focuses on analyzing evidence across the incident lifecycle, including initial access, system and network activity, possible data theft, and file-encryption behavior.


<h2> Lab Environment </h2>

| Component | Details |
|---|---|
| Host system | macOS |
| Virtualization platform | Oracle VirtualBox |
| Analyst VM | Ubuntu Linux |
| SIEM / log-analysis platform | Kibana / Elastic Stack |
| Data source | https://github.com/jirosgyros/soc-ctf-scanned-document-468/tree/main/data |
| Analysis interface | Kibana Discover |
| Project type | SOC incident investigation / ransomware case study |

> Note: Ubuntu was used as my analyst workstation in a VirtualBox VM running on a macOS host. I used Kibana to search, pivot across, correlate, and analyze the provided log data.
  
<h2> Tools and Utilities </h2>

| Tool / Utility | Purpose in This Project |
|---|---|
| macOS | Physical host operating system |
| Oracle VirtualBox | Virtualization platform used to run the Ubuntu analyst VM |
| Ubuntu Linux | Analyst workstation used to conduct the investigation |
| Kibana | Log search, filtering, pivoting, timeline analysis, and evidence review |
| Elasticsearch / Elastic Stack | Backend data platform supporting indexed log data in Kibana |
| KQL | Query language used to filter and investigate events in Kibana |
| Sysmon | Windows endpoint telemetry source used for process, file, registry, and network events |
| Windows Event Logs | Security and object-access evidence, including account activity and audit-log clearing |
| Proxy / web logs | Evidence for download, AWS S3 upload, and exfiltration activity |
| OSINT | Utilized sources such as VirusTotal, Mitre Att&ck's website, Google, and AI |

