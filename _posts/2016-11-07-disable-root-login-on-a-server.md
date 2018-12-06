For security reasons its neccessary to disable the root user login on servers.
Just look at `/var/log/auth.log` to see who tries to login.

## Add new user

```sh
adduser <name>
usermod -aG sudo <name>
```

**Test login with** `<name>`

## Disable root login

```sh
sudo vim /etc/ssh/sshd_config
```

Replace `PermitRootLogin yes` with `PermitRootLogin no`.

Restart `sshd` via `sudo service sshd restart`
