## Q4 — On which **host** did the initial infection occur?

### Investigation Steps

1. To identify the affected host and investigate potential follow-on activity, I pivoted to the Sysmon logs:

   ```spl
   sourcetype=erebus:sysmon
   ```

2. This sourcetype contains endpoint telemetry, including process creation, network activity, registry activity, DNS activity, and LSASS-access events.

3. I searched for events associated with the user `dana.k` and found a relevant log event at:

   ```text
   July 1, 2025 @ 4:02:12.456
   ```

4. The event showed the following details:

| Field | Value |
|---|---|
| Computer | `mer-wks-114.corp.local` |
| Event ID | `1` |
| Event Type | Process creation |
| Process | `wscript.exe` |

5. The hostname was identified in the `computer` field. Sysmon Event ID `1` confirms process creation, and `wscript.exe` indicates that Windows Script Host was involved in executing the malicious JavaScript file.

**Answer:** `mer-wks-114.corp.local`

---

## Q5 — What was the first **child process** the malicious script spawned?

### Investigation Steps

1. I continued investigating in the same Sysmon sourcetype:

   ```spl
   sourcetype=erebus:sysmon
   ```

2. The initial script execution was associated with this command-line activity:

   ```text
   C:\Windows\System32\explorer.exe "C:\Users\dana.k\Downloads\Document_Scan_468.js"
   ```

3. The parent process was identified as:

   ```text
   C:\Windows\explorer.exe
   ```

4. To locate child-process activity, I filtered for Sysmon process-creation events using:

   ```text
   EventID: 1
   ParentCommandLine: C:\Windows\explorer.exe
   Computer: mer-wks-114.corp.local
   ```

5. I found a subsequent process-creation event at:

   ```text
   July 1, 2025 @ 4:02:17.393
   ```

6. The child process was identified in both the `CommandLine` and `Image` fields.

| Field | Value |
|---|---|
| Image | `cmd.exe` |
| CommandLine | `cmd.exe` |
| Timestamp | `July 1, 2025 @ 4:02:17.393` |
