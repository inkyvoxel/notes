Notes about enumeration on [[Windows]].

Enumeration is the process of identifying and gathering information about a system, network, or application.

## Common commands

- `dir` prints files and folders in current directory.
- `type <filename>` prints contents of a text file.
- `icacls <filename>` prints detailed information about a file's permissions.
- `where /R C:\ <filename>` prints location of filename.
- `whoami` prints the current user.

## Unattended Windows Installations

Check for configuration files left behind by automated Windows installs for sensitive information:

- `C:\Unattend.xml`
- `C:\Windows\Panther\Unattend.xml`
- `C:\Windows\Panther\Unattend\Unattend.xml`
- `C:\Windows\system32\sysprep.inf`
- `C:\Windows\system32\sysprep\sysprep.xml`

## PowerShell history

Check PowerShell history for current user.

From `cmd.exe`:

```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

From PowerShell:

```
type $env.userprofile\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

## Saved user credentials

Print saved credentials:

```
cmdkey /list
```

Run command with saved credentials:

```
runas /savedcred /user:username cmd.exe
```

## IIS configuration

IIS is the default web server on Windows. Check for `web.config` on the target computer:

- `C:\inetpub\wwwroot\web.config`
- `C:\Windows\Microsoft.NET\Framework64\<version>\Config\web.config`

Find connection strings in `web.config`:

```
type web.config | findstr connectionString 
```

## PuTTY credentials

Print stored PuTTY proxy credentials from registry:

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

## Scheduled Tasks

Run `schtasks` to see which scheduled tasks are running.

Run `schtasks /query /tn <scheduled-task-name> /fo list /v` to see detailed information about a specific scheduled task.

Check if you can overwrite the file that is being ran.

Run `schtasks /run /tn <scheduled-task-name>` to force the task to run. You need the correct permissions to do this.

[[Windows/Privilege escalation#Scheduled tasks]]

## AlwaysInstallElevated

Check if `AlwaysInstallElevated` is enabled, which allows `.msi` files to run with elevated privileges, even on standard users.

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

[[Windows/Privilege escalation#AlwaysInstallElevated]]

## Windows Services

Run `sc qc <servicename>` to print more information about a service.

Check `BINARY_PATH_NAME` for executable. Run `icacls <filepath>` to check permissions. Is there modify `(M)` permissions?

Check `SERVICE_START_NAME` for user.

Check `HKLM\SYSTEM\CurrentControlSet\Services\` registries for service configurations.

## Related

- [[Windows]]
- [[Windows/Privilege escalation]]