# 🕵️ CTF Investigation Write-Up

---
## Q22 — The attacker ran ransomware on the domain controller. What on-disk **payload file** (DLL) did they execute to encrypt the files?

### Investigation Steps

1. I reviewed Sysmon process-creation events from the compromised domain controller:

   ```spl
   sourcetype=erebus:sysmon
   Computer=mer-dc01.corp.local
   EventID=1
   ```

2. Sysmon Event ID `1` records process-creation activity. I searched for events occurring after the attacker deleted volume shadow copies.

3. I identified a ransomware-related process-creation event at:

   ```text
   July 1, 2025 @ 05:58:09.655
   ```

4. The `CommandLine` field contained the following command:

   ```text
   rundll32.exe C:\ProgramData\microsoft\sysfunc.dll,#1 -lockername sysfunc
   ```

5. The command components indicate the following:

| Command Component | Meaning |
|---|---|
| `rundll32.exe` | Legitimate Windows utility used to load and execute a DLL |
| `C:\ProgramData\microsoft\sysfunc.dll` | On-disk DLL payload loaded by `rundll32.exe` |
| `,#1` | Calls the DLL export using ordinal number `1` |
| `-lockername sysfunc` | Ransomware-related argument associated with the payload |

6. The malicious DLL payload loaded from disk was `sysfunc.dll`.

| Field | Value |
|---|---|
| Host | `mer-dc01.corp.local` |
| Event ID | `1` |
| Loader | `rundll32.exe` |
| Payload Path | `C:\ProgramData\microsoft\sysfunc.dll` |
| Payload File | `sysfunc.dll` |
| Timestamp | `July 1, 2025 @ 05:58:09.655` |

**Answer:** `sysfunc.dll`

---

## Q23 — What MITRE ATT&CK **technique ID** describes that file-encryption step?

### Investigation Steps

1. The attacker executed a ransomware DLL that encrypted files on the compromised domain controller.

2. I reviewed the MITRE ATT&CK techniques under the **Impact** tactic for the technique describing adversaries encrypting data to disrupt availability and pressure victims.

3. The relevant MITRE ATT&CK technique is:

| MITRE ATT&CK Technique | Technique ID | Tactic |
|---|---|---|
| Data Encrypted for Impact | `T1486` | Impact |

**Answer:** `T1486`
