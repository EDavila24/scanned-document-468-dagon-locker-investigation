## Q20 — What **command** did the attacker run to delete volume shadow copies?

### Investigation Steps

1. I searched for process-creation events containing the term `delete` on the compromised domain controller:

   ```spl
   Computer=mer-dc01.corp.local
   EventID=1
   ```

2. I found a Sysmon process-creation event from the domain controller at:

   ```text
   July 1, 2025 @ 05:56:37.173
   ```

3. The `CommandLine` field contained the following command:

   ```text
   vssadmin delete shadows /all /quiet
   ```

4. This command deletes all volume shadow copies without prompting the user.

5. Attackers may delete shadow copies to limit recovery options before encrypting files or performing destructive activity.

| Field | Value |
|---|---|
| Host | `mer-dc01.corp.local` |
| Event ID | `1` |
| Command | `vssadmin delete shadows /all /quiet` |
| Tool | `vssadmin` |
| Timestamp | `July 1, 2025 @ 05:56:37.173` |

**Answer:** `vssadmin delete shadows /all /quiet`

---

## Q21 — Which Windows Security **Event ID** indicates the attacker cleared the event log?

### Investigation Steps

1. I reviewed the data dictionary to identify the Windows Security event associated with clearing audit logs.

2. Windows Security Event ID `1102` indicates that the audit log was cleared.

3. I filtered the Windows Security logs for this event ID:

   ```spl
   sourcetype=erebus:windows_security
   Computer=mer-dc01.corp.local
   EventID=1102
   ```

4. I identified the event on the previously compromised domain controller:

   ```text
   mer-dc01.corp.local
   ```

5. The event occurred at:

   ```text
   July 1, 2025 @ 05:57:38.315
   ```

| Field | Value |
|---|---|
| Host | `mer-dc01.corp.local` |
| Event ID | `1102` |
| Event Type | Audit log cleared |
| Timestamp | `July 1, 2025 @ 05:57:38.315` |

**Answer:** `1102`
