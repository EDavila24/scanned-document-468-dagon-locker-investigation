# 🕵️ CTF Investigation Write-Up

---

## Q6 — Which **LOLBin** (living-off-the-land binary) executed the dropped payload?

### Investigation Steps

1. I continued investigating Sysmon process-creation events from the infected endpoint:

   ```spl
   sourcetype=erebus:sysmon
   ```

2. I filtered for the affected host, `mer-wks-114.corp.local`, and Sysmon Event ID `1`, which records process-creation activity.

3. I reviewed events occurring after the previously identified child-process event to determine which built-in Windows binary executed the dropped payload.

4. A relevant process-creation event occurred at:

   ```text
   July 1, 2025 @ 4:02:24.350
   ```

5. The `CommandLine` field contained the following command:

   ```text
   rundll32.exe magni.w,scab \k arabika752
   ```

6. The executable `rundll32.exe` is a legitimate Windows utility that can be abused to execute malicious DLL exports, making it a commonly abused LOLBin. (Learned this from reserach)

| Field | Value |
|---|---|
| Host | `mer-wks-114.corp.local` |
| Event ID | `1` |
| Timestamp | `July 1, 2025 @ 4:02:24.350` |
| CommandLine | `rundll32.exe magni.w,scab \k arabika752` |
| LOLBin | `rundll32.exe` |

**Answer:** `rundll32.exe`

---

## Q7 — What is the **name** of the scheduled task created for persistence?

### Investigation Steps

1. To investigate persistence activity, I pivoted from Sysmon logs to Windows Security event logs:

   ```spl
   sourcetype=erebus:windows_security
   ```

2. I filtered for events from the affected host, `mer-wks-114.corp.local`, occurring after the `rundll32.exe` execution.

3. I searched for Windows Security Event ID `4698`, which records the creation of a scheduled task.

4. A scheduled-task creation event was identified at:

   ```text
   July 1, 2025 @ 04:02:44.632
   ```

5. The scheduled-task name was identified in the `TaskName` field.

| Field | Value |
|---|---|
| Host | `mer-wks-114.corp.local` |
| Event ID | `4698` |
| Event Type | Scheduled task created |
| Timestamp | `July 1, 2025 @ 04:02:44.632` |
| TaskName | `\MicrosoftUpdateSync` |

**Answer:** `\MicrosoftUpdateSync`
