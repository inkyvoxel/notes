Notes about enumeration on [[Linux]].

Enumeration is the process of identifying and gathering information about a system, network, or application.

Common commands for information gathering on the target:
- `hostname` prints the computer's name.
- `uname -a` prints information about the kernel.
- `cat /proc/version` prints additional information about the kernel, and sometimes compiler information.
- `cat /etc/issue` can be used to identify some operating systems.
- `ps` shows running processes.
- `env` prints environment variables.
- `sudo -l` prints commands the current user can run with `sudo`.
- `ls -la` shows all files and folders in current directory, including hidden files.
- `id` prints an overview of current user's privileges and groups.
- `cat /etc/passwd` prints information about all accounts configured on the system.
- `ifconfig` prints information about the network adapters.
- `cat /etc/exports` prints information about Network File Sharing (NFS)
- `echo $PATH` prints a list of directories used to search for binaries when commands are typed in the terminal.

## Related

- [[Automated enumeration]]