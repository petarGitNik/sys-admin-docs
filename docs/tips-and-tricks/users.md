# User Management

## Adding new user

Use `adduser` to add a user, and `usermod` to modify the user, and add it to the `sudo` group:

```bash
adduser <username>
usermod -aG sudo <username>
```

For advance use cases you can restrict which commands can a `sudo` user run. Also if you happen to need a root user, you can switch to it using `su` from any user[^1][^2][^3].

!!! warning
    Do not forget the `-a` flag! Without it, the user will be removed from all other groups it belongs to[^4][^5][^6].

## Passwordless sudo

If you want to use automation, you will have to give your sudo user ability to execute sudo without password. Securitywise, this is probably not the smartest thing you can do, but it gives you flexible server automation (you don't have to expose your password to third party software or commit it to source control).

To achieve this you must edit `/etc/sudoers` i.e. the file it includes. To edit these files use `visudo` comand.

_OK OK but which file should I edit?_ Last line in `/etc/sudoers` file deceitfully looks like a comment but it's actually not: `#includedir /etc/sudoers.d`. In this included directory you can find a file which you can edit to give your user ability to do sudo without password. Make a new file there (with `440` permissions), or use an existing one to edit. The existing one could have a filename such as `90-cloud-init-users` or similar. To edit it use the following:

```bash
sudo visudo -f /etc/sudoers.d/90-cloud-init-users
```

And append this line to the file:

```ini
exampleuser ALL=(ALL) NOPASSWD:ALL
```

[^1]: <https://www.a2hosting.com/kb/getting-started-guide/accessing-your-account/disabling-ssh-logins-for-root>
[^2]: <https://web.archive.org/web/20180331220811/https://www.a2hosting.com/kb/getting-started-guide/accessing-your-account/disabling-ssh-logins-for-root>
[^3]: <http://archive.is/Nntxg>
[^4]: <https://askubuntu.com/questions/2214/how-do-i-add-a-user-to-the-sudo-group>
[^5]: <https://web.archive.org/web/20180331221303/https://askubuntu.com/questions/2214/how-do-i-add-a-user-to-the-sudo-group>
[^6]: <http://archive.is/ZhXwS>
