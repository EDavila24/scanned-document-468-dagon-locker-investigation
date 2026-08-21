# 🕵️ CTF Investigation Write-Up

---

## Q2 — Which user **executed** the phishing attachment?


### Investigation Steps

1. Because the question references **phishing**, I began by searching the Proofpoint TAP email-security logs:

   ```spl
   sourcetype=erebus:proofpoint_tap
   ```

2. I identified a malicious inbound email with the following indicators:

   | Field | Value |
   |---|---|
   | **Sender** | `billing@doc-scan-portal.com` |
   | **Sender IP** | `192.0.2.255` |
   | **Recipient** | `dana.k@corp.median-grp.com` |
   | **Threat classification** | `MALWARE` |
   | **Threat URL** | [moashraya.com/scan/468](https://moashraya.com/scan/468) |
   | **Timestamp** | `2025-07-01 04:00:00.796` |

3. The recipient, **`dana.k`**, interacted with the malicious URL delivered through the phishing email.

> [!WARNING]
> The message was classified as **MALWARE** and contained a malicious URL.

>
4. After identifying the initial phishing email and its malicious URL, I pivoted to Windows process-creation logs to determine which user executed the phishing attachment.

5. In Kibana Discover, I filtered for process-creation events using **Sysmon Event ID 1** and **Windows Security Event ID 4688**:

   ```kql
   EventID: 1 OR EventID: 4688
   ```

6. I added the `CommandLine` and `SubjectUserName` fields as columns in the Discover results. The `SubjectUserName` field identified the account that launched the process, while `CommandLine` showed the executed command and associated file path.

7. The process-creation evidence showed that **`dana.k`** executed the phishing attachment.

> [!IMPORTANT]
> **Answer:** `dana.k`

---

## Q3 — What was the **filename** of the malicious attachment?


### Investigation Steps

1. Since the phishing email contained a URL, I pivoted to proxy-access logs to identify the user’s web activity:

   ```spl
   sourcetype=erebus:proxy_access
   ```

2. The proxy logs showed a successful request to `moashraya.com`.

   | Field | Value |
   |---|---|
   | **HTTP method** | `GET` |
   | **Domain** | `moashraya.com` |
   | **HTTP status** | `200 OK` |
   | **Malicious file** | `Document_Scan_468.js` |
   | **Timestamp** | `2025-07-01 04:01:31.692` |

Also here's the VirusTotal result for the url I found as malicous:
<img width="577" height="263" alt="Screenshot 2026-08-18 at 2 22 08 PM" src="https://github.com/user-attachments/assets/4c59534b-9598-440f-9383-91285ca4798f" />


4. The requested URL contained the JavaScript payload:

   ```text
   Document_Scan_468.js
   ```

> [!CAUTION]
> A `200 OK` response confirms that the proxy successfully retrieved the malicious JavaScript file.

> [!IMPORTANT]
> **Answer:** `Document_Scan_468.js`
