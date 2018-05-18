# Creating Users and Disabling SSH login for the `root` user

Before you disable SSH logins for the root account, you need to add new users.

## Wait, why should I disable the root account SSH login?

The moment your server is ready bots all around the world will start brute forcing and probing your server on every imaginable port and other entry points. They will scan whether you have `wp-admin/` on your URL (to crackdown on your Wordpress site if you have it), they will try various exploits and so on. One of the first things you should notice in your apache logs (you do read your logs, d-don't you?) is a bot brute forcing its way into the server. Or at least trying to. Yes, if you have a good enough password, their attempts will probably not work, but nonetheless, you should step up your security game and disable the SSH login for the root account.

## How do I do it?

### Adding new user

Use `adduser` to add a user, and `usermod` to modify the user, and add it to the `sudo` group:

```bash
adduser <username>
usermod -aG sudo <username>
```

For advanced use cases you can restrict which commands can a `sudo` user run. Also if you happen to need a root user, you can switch to it using `su` from any (`sudo`) user[^1][^2][^3].

!!! warning
    Do not forget the `-a` flag! Without it, the user will be removed from all other groups it belongs to[^4][^5][^6].

### Disabling SSH login for the `root` user

These steps were taken from the reference[^1][^2][^3]. For the original instructions visit the original link. Now proceed to disable the SSH login for the `root` user:

```shell
sudo vi /etc/ssh/sshd_config
```

Find the line which contains `PermitRootLogin` directive. Modify it to match:

```ini
PermitRootLogin no
```

Add the following line:

```ini
AllowUsers <username>
```

!!! note
    If you have more than one user, you can add them separated by space e.g. `AllowUsers johnsmith janesmith`.

Save the changes and restart the SSH daemon.

```bash
service ssh restart
```

That's it.

!!! warning
    Before you log out from `root` SSH session, make sure that you can SSH with the new user in a separate terminal window. If you can't login, go back to `/etc/ssh/sshd_config` and debug the mistake. If you exist from the session, and you can't log in with your new user, bad luck.

[^1]: <https://www.a2hosting.com/kb/getting-started-guide/accessing-your-account/disabling-ssh-logins-for-root>
[^2]: <https://web.archive.org/web/20180331220811/https://www.a2hosting.com/kb/getting-started-guide/accessing-your-account/disabling-ssh-logins-for-root>
[^3]: <http://archive.is/Nntxg>
[^4]: <https://askubuntu.com/questions/2214/how-do-i-add-a-user-to-the-sudo-group>
[^5]: <https://web.archive.org/web/20180331221303/https://askubuntu.com/questions/2214/how-do-i-add-a-user-to-the-sudo-group>
[^6]: <http://archive.is/ZhXwS>
