## Q24 — Taking the encryption together with the recovery-inhibition step that immediately preceded it, what **class of attack** is this incident?

### Investigation Steps

1. In Q20, the attacker executed the following command to delete volume shadow copies:

   ```text
   vssadmin delete shadows /all /quiet
   ```

2. This action inhibits recovery by removing available volume shadow copies.

3. In Q22, the attacker executed the ransomware payload `sysfunc.dll` using `rundll32.exe` to encrypt files.

4. The combination of deleting recovery mechanisms and encrypting files is characteristic of a ransomware attack.

| Observed Activity | Purpose |
|---|---|
| `vssadmin delete shadows /all /quiet` | Prevents or limits file recovery |
| `rundll32.exe ... sysfunc.dll` | Executes the file-encryption payload |
| File encryption | Disrupts access to victim data |

**Answer:** `Ransomware`

---

## Q25 — What **ransomware family** does this intrusion attribute to?

### Investigation Steps

1. I pivoted on ransomware indicators identified during the encryption activity:

| Indicator | Value |
|---|---|
| Payload Path | `C:\ProgramData\microsoft\sysfunc.dll` |
| Payload File | `sysfunc.dll` |
| Loader | `rundll32.exe` |
| Locker Argument | `-lockername sysfunc` |

2. To identify the extension added to encrypted files, I searched for Sysmon Event ID `11` file-creation events occurring after the ransomware payload executed.

   ```spl
   sourcetype=erebus:sysmon
   Computer=mer-dc01.corp.local
   EventID=11
   ```

3. I found four file-creation events within approximately one minute of the encryption command.

4. Each newly created file used the same extension:

   ```text
   .dagoned
   ```

5. The combined indicators—`sysfunc.dll`, `-lockername sysfunc`, the `rundll32.exe` execution method, and the `.dagoned` encrypted-file extension—attribute the intrusion to the Dagon Locker ransomware family.

| Attribution Indicator | Value |
|---|---|
| Payload File | `sysfunc.dll` |
| Locker Argument | `sysfunc` |
| Encrypted File Extension | `.dagoned` |
| Ransomware Family | `Dagon Locker` |

**Answer:** `Dagon Locker`
