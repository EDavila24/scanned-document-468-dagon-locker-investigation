## Q1 — How many distinct `sourcetype`s are present in the index?

### Investigation Steps

1. I examined a sample event from the index and identified the `st` field, which represents the event’s **sourcetype**.

2. I then reviewed the available log sources documented in the GitHub repository and identified the following distinct sourcetypes:

| # | Sourcetype | Description |
|---:|---|---|
| 1 | `erebus:sysmon` | erebus:sysmon	Endpoint process / network / registry / DNS / LSASS-access telemetry |
| 2 | `erebus:windows_security` | Windows Security event logs |
| 3 | `erebus:proxy_access` | Web/proxy traffic — HTTP requests and downloads |
| 4 | `erebus:zeek_dns` | Network-level DNS telemetry (Zeek sensor) |
| 5 | `erebus:zeek_conn` | Network-level connection/flow telemetry (Zeek sensor) |
| 6 | `erebus:panos` | Perimeter firewall telemetry |
| 7 | `erebus:proofpoint_tap` | Email-security gateway — inbound message delivery |

3. Afterwards, I confirmed there are seven distinct sourcetypes represented in the index.

> [!IMPORTANT]
> **Answer:** `7` distinct sourcetypes
