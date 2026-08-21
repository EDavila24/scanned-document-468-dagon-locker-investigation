# 🕵️ CTF Investigation Write-Up

---
## Q11 — Name one **Active Directory reconnaissance** tool or command run from the beacon.

### Investigation Steps

1. I continued reviewing process-creation events from the infected host after the beacon established its network connection.

2. Active Directory reconnaissance activity is often visible in process-creation logs and the `CommandLine` field.

3. I identified the following command in a Sysmon process-creation event:

   ```text
   nltest /dclist:corp
   ```

4. This command uses the Windows utility `nltest` to list domain controllers in the `corp` domain. Attackers may use this information to map the Active Directory environment before attempting lateral movement or privilege escalation.

5. The event occurred at:

   ```text
   July 1, 2025 @ 04:06:12.458
   ```

| Field | Value |
|---|---|
| Host | `mer-wks-114.corp.local` |
| Tool | `nltest` |
| CommandLine | `nltest /dclist:corp` |
| Purpose | Lists domain controllers for the specified domain |
| Timestamp | `July 1, 2025 @ 04:06:12.458` |

**Answer:** `nltest`
