## Q12 — Which **domain controller** did the attacker access?

### Investigation Steps

1. I continued reviewing Sysmon process-creation events from the infected host:

   ```spl
   sourcetype=erebus:sysmon
   Computer=mer-wks-114.corp.local
   ```

2. Shortly after the prior reconnaissance activity, the attacker ran the following command to enumerate privileged Active Directory users:

   ```text
   net group "Domain Admins" /domain
   ```

3. This command lists members of the **Domain Admins** group and helps an attacker identify accounts with high-level administrative privileges.

4. Thirteen seconds later, I identified another Active Directory reconnaissance command:

   ```text
   AdFind.exe -f "(objectcategory=person)" -csv
   ```

5. This `AdFind.exe` command searches Active Directory for person-related objects and exports the results in CSV format.

6. I then identified a remote execution command in the `CommandLine` field:

   ```text
   wmic /node:MER-DC01 /user:CORP\t.reyes process call create "rundll32 C:\ProgramData\update.dll,......"
   ```

7. The `/node:MER-DC01` argument identifies the remote computer targeted by the attacker.

8. The event occurred at:

   ```text
   July 1, 2025 @ 04:10:23.375
   ```

| Field | Value |
|---|---|
| Source Host | `mer-wks-114.corp.local` |
| Target Domain Controller | `MER-DC01` |
| Remote Execution Tool | `wmic.exe` |
| Payload | `C:\ProgramData\update.dll` |
| Timestamp | `July 1, 2025 @ 04:10:23.375` |

**Answer:** `MER-DC01`

---

## Q13 — What **mechanism** did the attacker use to execute the beacon on the domain controller?

### Investigation Steps

1. I reviewed the same Sysmon event that showed the attacker remotely targeting the domain controller.

2. The malicious command used Windows Management Instrumentation Command-line, commonly known as WMIC, to create a process remotely on `MER-DC01`.

3. The following fields identified the executable responsible for the remote execution:

| Field | Value |
|---|---|
| ParentImage | `WMIC.exe` |
| OriginalFileName | `WMIC.exe` |

4. The attacker used `WMIC.exe` with `process call create` to create and execute a process remotely on the domain controller.

**Answer:** `WMIC.exe`
