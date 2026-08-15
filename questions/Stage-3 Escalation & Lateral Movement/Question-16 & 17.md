## Q16 — What is the **name** of the hidden local admin account the attacker created?

### Investigation Steps

1. I continued reviewing the Sysmon and Windows Security logs associated with the compromised file server:

   ```spl
   sourcetype=erebus:windows_security
   Computer=mer-fs01.corp.local
   EventID=4720
   ```

2. Windows Security Event ID `4720` records the creation of a user account.

3. Less than one minute after the AnyDesk installation activity, I identified an account-creation event.

4. The `TargetUserName` field contained the name of the newly created local account.

| Field | Value |
|---|---|
| Host | `mer-fs01.corp.local` |
| Event ID | `4720` |
| Event Type | User account created |
| TargetUserName | `oldadministrator` |
| Timestamp | `July 1, 2025 @ 04:24:21.961` |

**Answer:** `oldadministrator`

---

## Q17 — What **file** did the attacker open on a network share to harvest credentials?

### Investigation Steps

1. To identify files accessed by the attacker, I searched for Windows Security Event ID `4663`.

   ```spl
   sourcetype=erebus:windows_security
   EventID=4663
   ```

2. Windows Security Event ID `4663` records an attempt to access an object, such as a file or folder.

3. The activity occurred after the attacker performed Active Directory reconnaissance and began moving through the environment.

4. A relevant object-access event occurred at:

   ```text
   July 1, 2025 @ 04:07:39.559
   ```

5. The `ObjectName` field identified the file accessed on the network share:

   ```text
   \\MER-FS01\Finance\passwords.xlsx
   ```

6. This file likely contained credentials or sensitive financial information, explaining the attacker’s interest in the file server.

| Field | Value |
|---|---|
| Event ID | `4663` |
| Event Type | Object access |
| Network Share | `\\MER-FS01\Finance` |
| File Accessed | `passwords.xlsx` |
| Full ObjectName | `\\MER-FS01\Finance\passwords.xlsx` |
| Timestamp | `July 1, 2025 @ 04:07:39.559` |

**Answer:** `passwords.xlsx`
