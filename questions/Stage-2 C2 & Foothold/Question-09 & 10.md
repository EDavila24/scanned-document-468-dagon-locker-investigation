# 🕵️ CTF Investigation Write-Up

---
## Q9 — To what **path on disk** was the second-stage payload saved?

### Investigation Steps

1. I reviewed the same PowerShell process-creation event identified in the previous question.

2. The relevant command was located in the `CommandLine` field.

3. At the end of the command line, I identified the local path used to save the downloaded second-stage payload.

| Field | Value |
|---|---|
| Process | `powershell.exe` |
| Payload path | `C:\ProgramData\update.dll` |
| Timestamp | `July 1, 2025 @ 04:04:45.383` |

**Answer:** `C:\ProgramData\update.dll`

---

## Q10 — Name one **command-and-control (C2) destination** the beacon contacted.

### Investigation Steps

1. To identify outbound network connections made by the beacon, I filtered Sysmon logs for Event ID `3`.

   ```spl
   sourcetype=erebus:sysmon
   Computer=mer-wks-114.corp.local
   EventID=3
   ```

2. Sysmon Event ID `3` records network-connection activity.

3. I found a network connection event from the infected host at:

   ```text
   July 1, 2025 @ 04:05:10.584
   ```

4. The event contained the following network details:

| Field | Value |
|---|---|
| Host | `mer-wks-114.corp.local` |
| Event ID | `3` |
| Source IP | `10[.]25[.]219[.]184` |
| Destination IP | `51[.]89[.]133[.]3` |
| Timestamp | `July 1, 2025 @ 04:05:10.584` |

5. The external destination IP is a command-and-control destination contacted by the beacon.

**Answer:** `51[.]89[.]133[.]3`
