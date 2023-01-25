There are typically two types of users on [[Windows]]:

- Administrators
- Standard users

Users with administrative permissions are added to the `Administrators` group.

Windows has some special accounts which are created and managed by the operating system:

| Name            | Description                                                                            |
| --------------- | -------------------------------------------------------------------------------------- |
| SYSTEM          | Has full access to files and resources. Higher privilege than Administrators group.    |
| LocalSystem     | Similar to `SYSTEM`.                                                                   |
| Local Service   | Runs services with 'minimum' permissions. Uses anonymous connections over the network. |
| Network Service | Similar to `Local Service`, but uses computer credentials to authenticate on network.  |
