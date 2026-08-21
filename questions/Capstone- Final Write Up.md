# 🕵️Capstone Incident Assessment
## Scanned Document 468 — Dagon Locker Ransomware Investigation

---

## Executive Summary

| Classification | Assessment |
|---|---|
| **Incident type** | Double-extortion ransomware intrusion |
| **Ransomware family** | Dagon Locker |
| **Likely motivation** | Financial gain |
| **Named APT attribution** | Not supported by the available evidence |
| **Likely operator type** | Financially motivated ransomware operator or RaaS affiliate |
| **Initial access** | Phishing email and malicious JavaScript attachment |
| **Primary impact** | Data exfiltration followed by file encryption |
| **Key encryption indicator** | `.dagoned` file extension |

I determined the incident is a **Dagon Locker double-extortion ransomware intrusion**. The attacker gained initial access through phishing, performed discovery and lateral movement, established persistence, exfiltrated sensitive data to cloud service, inhibited recovery and forensic investigation, and then encrypted files.

The attacker’s objective was most likely to create financial pressure: steal sensitive data, encrypt organizational files, and demand payment for both decryption and non-disclosure of the stolen information.

---

## Incident Scope

| Category | Known Evidence |
|---|---|
| Initial user | `dana.k` |
| Initial malicious file | `.Document_Scan_468.js` |
| Initial execution process | `explorer.exe` |
| Payload execution utility | `rundll32.exe` |
| Second-stage delivery service | `file.io` |
| Downloaded payload | `C:\ProgramData\update.dll` |
| Command-and-control IP | `51.89.133.3` |
| File server accessed | `mer-fs01.corp.local` |
| Sensitive file accessed | `passwords.xlsx` |
| Archive staged for transfer | `fin_eng.zip` |
| Exfiltration utility | `rclone.exe` |
| Exfiltration destination | Amazon Web Services (`amazonaws`) |
| Persistence / remote access | AnyDesk |
| Recovery inhibition | `vssadmin.exe` |
| Defense evasion | Event-log clearing |
| Encryption payload | `sysfunc.dll` launched by `rundll32.exe` |
| Encryption indicator | `.dagoned` file extension |

---

## Attack Timeline


| Phase | Time | Host / Account | Observed Activity | Evidence |
|---|---|---|---|---|
| 1. Phishing delivery | `[July 1, 2025 @ 4:00:00.796]` | `dana.k` mailbox | Phishing email delivered to the user | Email telemetry |
| 2. Initial execution | `[July 1, 2025 @ 4:01:31.692]` | `[mer-wks-114.corp.local]` / `dana.k` | User opened `.Document_Scan_468.js` from `explorer.exe` | Process-creation telemetry |
| 3. Payload execution | `[July 1, 2025 @ 4:02:24.350]` | `[mer-wks-114.corp.local]` | `rundll32.exe` executed attacker payload | Process command line |
| 4. Second-stage download | `[Jul 1, 2025 @ 04:04:45.383]` | `[mer-wks-114.corp.local]` | Payload retrieved from `file.io` and saved as `C:\ProgramData\update.dll` | Proxy and file-creation telemetry |
| 5. C2 communication | `[July 1, 2025 @ 04:05:10.584]` | `[mer-wks-114.corp.local]` | Connection established with `51.89.133.3` | Network or proxy telemetry |
| 6. Domain discovery | `[July 1, 2025 @ 04:06:12.458] , [July 1, 2025 @ 04:06:21.035], & [July 1, 2025 @ 04:06:34.079]` | `[mer-wks-114.corp.local]` / `[dana.k]` | `nltest`, `net group "Domain Admins" /domain`, and `AdFind.exe` executed | Process-creation telemetry |
| 7. Lateral movement | `[July 1, 2025 @ 04:10:23.375]` | `[mer-wks-114.corp.local]` → `mer-dc01.corp.local` / `[t.reyes]` | Attacker accessed domain controller | Authentication and endpoint telemetry |
| 8. Domain-controller activity | `[July 1, 2025 @ 04:10:23.375]` | `[mer-dc01.corp.local]` / `[t.reyes]` | `WMIC.exe` used against domain-controller infrastructure | Process and remote-management telemetry |
| 9. Persistence | `[July 1, 2025 @ 04:23:50.099]` | `[mer-fs01.corp.local]` / `[t.reyes]` | AnyDesk installed for remote access and persistence | File, service, registry, or process telemetry |
| 10. Data collection | `[July 1, 2025 @ 04:07:39.559]` | `mer-fs01.corp.local` / `[dana.k]` | Sensitive data, including `passwords.xlsx`, accessed and staged | File-access telemetry |
| 11. Exfiltration | `[July 1, 2025 @ 05:26:30.728]` | `[mer-fs01.corp.local]` / `[t.reyes]` | `rclone.exe` uploaded `fin_eng.zip` to AWS; HTTP `PUT` returned `200` | Proxy / web telemetry |
| 12. Recovery inhibition | `[July 1, 2025 @ 05:56:37.173]` | `[mer-dc01.corp.local]` / `[t.reyes]` | `vssadmin.exe` deleted Volume Shadow Copies | Process command line |
| 13. Defense evasion | `[July 1, 2025 @ 05:57:38.315]` | `[mer-dc01.corp.local]` / `[t.reyes]` | Windows event logs cleared | Windows Security / event-log telemetry |
| 14. Encryption | `[July 1, 2025 @ 05:58:09.655]` | `[mer-dc01.corp.local]` | `rundll32.exe` launched `sysfunc.dll`; files received `.dagoned` extension | Process and file telemetry |

---

## Attack Story

### 1. Initial Access

This attack began when a phishing email was delivered to **`dana.k`’s mailbox** on **`[July 1, 2025 @ 4:00:00.796]`**. Then the user opened the malicious JavaScript attachment, `.Document_Scan_468.js`, on **`[mer-wks-114.corp.local]`** at **`[July 1, 2025 @ 4:01:31.692]`**.

The file was launched from `explorer.exe`. The attack then progressed to `rundll32.exe`, which executed the attacker’s payload.

```text
explorer.exe
└── .Document_Scan_468.js
    └── rundll32.exe
```


### 2. Payload Delivery and C2

Following execution, the attacker downloaded a second-stage payload from the file-sharing service `file.io`. The payload was written to:

```text
C:\ProgramData\update.dll
```

The compromised host then communicated with **`51.89.133.3`**, indicating probable command-and-control activity.

```text
file.io
└── C:\ProgramData\update.dll
    └── C2 communication: 51.89.133.3
```


### 3. Discovery and Reconnaissance

The attacker performed Active Directory discovery to map/recon the environment, identify privileged accounts, and locate key systems. Observed reconnaissance tools and commands included:

```text
nltest
net group "Domain Admins" /domain
AdFind.exe
```

This activity is consistent with attacker transitioning to lateral movement and access to sensitive resources (found what each tool did through research).

| Tool / Command | Likely Purpose |
|---|---|
| `nltest` | Identify domain and trust information |
| `net group "Domain Admins" /domain` | Enumerate privileged domain-group members |
| `AdFind.exe` | Query Active Directory objects, users, groups, computers, and domain structure |

### 4. Lateral Movement and Domain Activity

The attacker moved from **`[mer-wks-114.corp.local]`** to the domain controller **`mer-dc01.corp.local`** using account **`[dana.k]`**

The attacker also used `WMIC.exe` to interact with domain-controller. 
WMIC can be abused as a built-in remote-management mechanism to run commands, gather system information, move laterally, and potentially establish persistence (found from research)



### 5. Persistence

The attacker installed **AnyDesk**, a legitimate remote-access tool (found from research). In this intrusion, the installation likely provided persistent remote access for controlling a the system.


### 6. Collection and Exfiltration

After reaching `mer-fs01.corp.local`, the attacker accessed sensitive data, including:

```text
passwords.xlsx
```

The attacker then staged data into the archive:

```text
fin_eng.zip
```

Using `rclone.exe`, the attacker transferred the archive to Amazon Web Services. A successful HTTP `PUT` request with a `200` response associated with `fin_eng.zip` confirms the transfer succeeded.

```text
passwords.xlsx
└── fin_eng.zip
    └── rclone.exe
        └── Amazon Web Services upload (HTTP PUT 200)
```

This data theft happened before encryption, which is the evidence supporting a **double-extortion** classification.

### 7. Defense Evasion and Recovery Inhibition

Before encrypting files, the attacker attempted to slow down incident response and recovery:

| Action | Tool / Technique | Intended Effect |
|---|---|---|
| Delete Volume Shadow Copies | `vssadmin.exe` | Make recovery of encrypted data more difficult |
| Clear Windows event logs | Event-log clearing | Reduce visibility and conceal attacker activity |

### 8. Encryption and Impact

The final stage involved execution of `sysfunc.dll` using `rundll32.exe`. The malware encrypted files and added the `.dagoned` extension.

```text
rundll32.exe
└── sysfunc.dll
    └── File encryption
        └── .dagoned extension
```

This encryption behavior, combined with the observed pre-encryption data exfiltration, supports iidentifying the incident as Dagon Locker double-extortion ransomware activity.

---


## Attribution Assessment

| Question | Assessment |
|---|---|
| Is a malware family identifiable? | Yes — evidence is consistent with Dagon Locker |
| Is the incident attributable to a named APT group? | No — the available evidence does not support named-APT attribution |
| What actor type is most likely? | Financially motivated ransomware operator or RaaS affiliate |
| Why? | The intrusion prioritized data theft, recovery inhibition, encryption, and extortion leverage |

The evidence supports attribution to the **Dagon Locker ransomware family**, based on the `sysfunc.dll` execution chain and the `.dagoned` encrypted-file extension. However, there isn't evidence to connect the attack to a specific named APT group.

The best conclusion is that the attacker was a **financially motivated cybercriminal**, potentially an affiliate operating within a Ransomware-as-a-Service model. Dagon Locker has been reported as a double-extortion ransomware operation, with data theft occurring with file encryption. 


---

## References

- [Original Scanned Document 468 SOC CTF Challenge](https://github.com/jirosgyros/soc-ctf-scanned-document-468)
- [Dagon Locker Ransomware Background](https://www.pcrisk.com/removal-guides/24976-dagon-locker-ransomware)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [VirusTotal](https://www.virustotal.com/gui/home/upload)
