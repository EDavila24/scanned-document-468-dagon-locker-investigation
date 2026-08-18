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

## Takeaway

We can always learn what we could've done/thought differently. The goal is not just to find a matching event, but to determine whether the available logs/sources supports a reliable explanation of what happened.

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
- Remote-access tools require context-based analysis because they may be legitimate administrative software or abused for persistence and remote control.

## Analyst Development Takeaways

- I improved my ability to translate raw logs into a clear incident attack chain.
- I gained experience documenting timestamps, hosts, users, processes, command lines, network indicators, and outcomes in a reproducible format.
- I developed a stronger understanding of how ATT&CK supports incident communication and detection planning.


## Final Reflection

This CTF helped me learn and understand the 'bigger picture' or the 'full story' like an analyst: reconstructing an incident, finding evidence, pivoting when necessary, and identifying/isolating/filtering certain key fields/soucres.
