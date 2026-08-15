## Q8 — From which **file-sharing service** was the second-stage beacon downloaded?

### Investigation Steps

1. To identify where the second-stage payload was downloaded from, I continued reviewing process-creation activity on the infected host:

   ```spl
   sourcetype=erebus:sysmon
   Computer=mer-wks-114.corp.local
   (EventID=1 OR EventID=4688)
   ```

2. I focused on events occurring after the scheduled task was created to identify commands executed by either `rundll32.exe` or the persistence mechanism.

3. A relevant process-creation event occurred at:

   ```text
   July 1, 2025 @ 04:04:45.383
   ```

4. The `CommandLine` field showed that `powershell.exe` was used to retrieve content from the following file-sharing service:

   ```text
   file.io
   ```

| Field | Value |
|---|---|
| Host | `mer-wks-114.corp.local` |
| Process | `powershell.exe` |
| Timestamp | `July 1, 2025 @ 04:04:45.383` |
| File-sharing service | `file.io` |

**Answer:** `file.io`
