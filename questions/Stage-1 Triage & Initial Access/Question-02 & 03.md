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
>  [!IMPORTANT]
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

3. The requested URL contained the JavaScript payload:

   ```text
   Document_Scan_468.js
   ```

> [!CAUTION]
> A `200 OK` response confirms that the proxy successfully retrieved the malicious JavaScript file.

> [!IMPORTANT]
> **Answer:** `Document_Scan_468.js`
