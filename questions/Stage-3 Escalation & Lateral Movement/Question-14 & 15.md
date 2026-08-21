# 🕵️ CTF Investigation Write-Up

---
## Q14 — How many **minutes** after the first beacon check-in was the domain controller reached?

### Investigation Steps

1. The first beacon network check-in was identified in the Sysmon network-connection event from Q10:

   ```text
   July 1, 2025 @ 04:05:10.584
   ```

2. The attacker accessed the domain controller in the remote WMIC execution event from Q12:

   ```text
   July 1, 2025 @ 04:10:23.375
   ```

3. The elapsed time between the first beacon check-in and domain-controller access was approximately:

   ```text
   5 minutes
   ```

| Event | Timestamp |
|---|---|
| First beacon check-in | `July 1, 2025 @ 04:05:10.584` |
| Domain controller accessed | `July 1, 2025 @ 04:10:23.375` |
| Approximate elapsed time | `5 minutes` |

**Answer:** `5 minutes`

---

## Q15 — What **remote-access tool** did the attacker install for persistence?

### Investigation Steps

1. To identify a possible remote-access tool used for persistence, I reviewed both Sysmon and Windows Security logs:

   ```spl
   sourcetype=erebus:sysmon OR sourcetype=erebus:windows_security
   ```

2. I searched for activity involving process creation, scheduled tasks, remote execution, and successful logons.

3. The attacker targeted the file server using a remote-execution command that referenced:

   ```text
   /node:MER-FS01
   ```

4. I also identified a successful logon event, Windows Security Event ID `4624`, associated with:

   ```text
   mer-fs01.corp.local
   ```

5. A subsequent process-creation event showed the following command:

   ```text
   C:\ProgramData\AnyDesk.exe --install C:\ProgramData\AnyDesk --silent --start-with-win
   ```

6. The command installs AnyDesk silently and configures it to start with Windows, providing persistence and remote access to the affected system.

| Field | Value |
|---|---|
| Target Host | `mer-fs01.corp.local` |
| Remote Target | `MER-FS01` |
| Process | `AnyDesk.exe` |
| Install Path | `C:\ProgramData\AnyDesk` |
| Persistence Option | `--start-with-win` |
| Timestamp | `July 1, 2025 @ 04:23:50.099` |

**Answer:** `AnyDesk.exe`
