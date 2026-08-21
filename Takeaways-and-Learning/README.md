# What I Learned

During this CTF, I practiced using Kibana to investigate activity across several log sources and to turn individual events into understanding an attack chain and connected the dots.


## Approach Used

1. **Start with an initial indicator**
   - I began with the available question & context, suspicious artifact, host, user, filename, process, or time range.

2. **Establish a timeline**
   - I reviewed events immediately before and after the initial 'red flag' to understand what occurred and identify related activity to answer the questions.

3. **Pivot across evidence**
   - I pivoted using fields such as hostname(computer), username, parent and child processes, command lines, file paths, IP addresses, domains, and timestamps.

4. **Validate findings**
   - I avoided relying on a single event when possible. I looked for connecting pieces from other sources, such as Sysmon, Windows Security logs, proxy logs, file-access events, and network records.

5. **Assess attacker behavior**
   - I classified confirmed actions by their purpose, such as execution, discovery, persistence, lateral movement, credential access, collection, exfiltration, or impact.

6. **Document the conclusion**
   - For each finding, I recorded the evidence, relevant query or filter, timeline position, impact, and other things that may have stuck out.

## 🎯 Key Takeaways

This investigation reinforced that effective incident analysis is more than finding a single matching log event. The goal is to determine whether the available evidence and log sources support an evidence based explanation of what happened.

With each investigation, it is an opportunity to improve research habits, pivoting, and the ability to connect activity across endpoint, network, and security logs.

### Skills and Concepts Reinforced

- **Reconnaissance tooling and commands:** Attackers may use legitimate Windows utilities, scripts, and purpose-built tools to enumerate systems, users, services, network connections, domain information, and security controls.

- **Windows Event Log analysis:** Different Windows Event IDs can provide evidence of authentication, process execution, privilege use, service activity, file access, and other relevant behavior. Selecting the right log source and Event ID is essential for validating a hypothesis.

- **AnyDesk (`AnyDesk.exe`):** AnyDesk is a legitimate remote-access tool, but it can be abused by attackers to maintain remote access, control an endpoint, or bypass normal administrative access paths.

- **Rclone (`rclone.exe`):** Rclone is a legitimate command-line tool for **copying/syncing files** to cloud storage). BUT, attackers may abuse it to transfer or exfiltrate data to external cloud-storage services.

- **Volume Shadow Copy deletion (`vssadmin`):** Attackers may use `vssadmin` commands to delete Volume Shadow Copies. This can make it difficult for restoring files and is commonly tied to ransomware activity.

- **DLL abuse:** Dynamic-Link Libraries (DLLs) are legitimate Windows components and are not straight up malicious. However, attackers can abuse DLLs to execute code within trusted processes, establish persistence, evade defenses, steal/modify data, and potentially elevate privileges.


## Core Analysis Practices

- Filtered events by time range to reduce noise and establish a reasonable timeline for events.
- Pivoted on usernames, Event IDs, endpoints, process names, command lines, file paths, IP addresses, domains, and filenames.
- Used parent-child process relationships to identify suspicious execution chains.
- Reviewed Windows and endpoint telemetry for execution, file creation, registry changes, and network connections.
- Correlated proxy or network evidence with endpoint activity to validate external communication and potential data exfiltration.
- Observed suspicious administrative behavior (after escalation) by considering context, timing, execution source, and connected indicators.
- Recorded notes and evidence (through Obseidan) so results could be referenced and validated.

## Technical Takeaways

- A ransomware investigation may involve many stages before encryption occurs. Initial access, reconnaissance, persistence, lateral movement, credential access, collection, and exfiltration.
- Process-command-line 'field' visibility is critical for understanding how suspicious tools were executed and what they were used to do.
- Parent-child process relationships help understand activity from suspicious execution chains.
- Remote-access tools require analysis because they may be legitimate administrative software or abused for persistence and remote control.

## Analyst Development Takeaways

- I improved my ability to translate raw logs into a clear incident attack chain.
- I gained experience documenting timestamps, hosts, users, processes, command lines, network indicators, and outcomes in a reproducible format.
- I developed a stronger understanding of how ATT&CK and OSINT supports incident communication and detection planning.


## Final Reflection

This CTF helped me learn and understand the 'bigger picture' or the 'full story' like an analyst: reconstructing an incident, finding evidence, pivoting when necessary, and identifying/isolating/filtering certain key fields/sources.
