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

6. The executable `rundll32.exe` is a legitimate Windows utility that can be abused to execute malicious DLL exports, making it a commonly abused LOLBin.

| Field | Value |
|---|---|
| Host | `mer-wks-114.corp.local` |
| Event ID | `1` |
| Timestamp | `July 1, 2025 @ 4:02:24.350` |
| CommandLine | `rundll32.exe magni.w,scab \k arabika752` |
| LOLBin | `rundll32.exe` |

**Answer:** `rundll32.exe`
