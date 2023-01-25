Notes about enumeration on [[Windows]].

Enumeration is the process of identifying and gathering information about a system, network, or application.

## Common commands

- `dir` prints files and folders in current directory.
- `type` prints contents of a text file.

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

## Related

- [[Windows]]