# Working With SSH Keys

## Generate a key

If you want to generate a single keypair your can use:

```bash
ssh-keygen -t rsa
```

Follow the instructions that are presented and you'll setup keypair. You may use passphrase if you want to have additional security, but keep in mind that passphrase will hinder you in automation later. Having key by itself is already far more greater security than using SSH with password. Also, passphrase generated for key is not same thing as the password used for SSH.

!!! warning
    When choosing a path for a new key be careful, do not overwrite existing one.

## Copy key to remote server

You can copy key easily using:

```bash
ssh-copy-id <USER>@<REMOTE_IP>
```

This will append the key to specified user in `~/.ssh/authorized_keys`. If you're not sure what's going to happen use `-n` switch for a dry run.

## Working with multiple keys

Examples above are not that much practical. In practice, you will want to have a new keypair for each new server/service. Therefore you will need to generate multiple keypairs. You can do it like this:

```bash
ssh-keygen -t rsa -f ~/.ssh/id_rsa.dbserver
ssh-keygen -t rsa -f ~/.ssh/id_rsa.appserver
```

Those keys can be copied to remote as:

```bahs
ssh-copy-id -i ~/.ssh/id_rsa.dbserver <USER>@<REMOTE_IP>
ssh-copy-id -i ~/.ssh/id_rsa.appserver <USER>@<REMOTE_IP>
```

When using multiple keys, you must edit SSH configuration file and set which key should be used for which host.

For more see [this][1][^1][^2].

## SSH config file

Each time you SSH into server you must type:

```bash
ssh <USER>@<REMOTE_IP>
```

Sooner or later this becomes boring, and it's hard to remember which user and server address you must use. Fortunately you can create SSH configuration file which can add alias for a host, and define which key to use when accessing to it. Here is an example of working configuration:

```
Host dbserver
Hostname <IP_ADDRESS>
IdentityFile ~/.ssh/id_rsa.dbserver
User <REMOTE_USER>

Host appserver
Hostname <IP_ADDRESS>
IdentityFile ~/.ssh/id_rsa.appserver
User <REMOTE_USER>
```

Config file should be located in `~/.ssh/config`. If the file is missing, make one and edit it to your needs.

Now you can ssh into server using alias:

```bash
ssh dbserver
ssh appserver
```

You can also use these aliases when using `scp` and `rsync`.

## Help, my SSH connection is breaking all the time!

`Write Failed: broken pipe`, eh? This is probably because you've did not set the SSH to keep the connection alive. Add this to your `config` file:

```
Host *
ServerAliveInterval 120
ServerAliveCountMax 2
```

You can set this either on client or server (see `ServerAliveInterval` and `ClientAliveInterval`). That's up to you. I have it set on my client since it was more easier and logical to have it set on my laptop instead of editing `config` file on each server.

For more see [this][2][^3][^4].

## Where can I find out more about SSH config file?

```bash
man ssh_config
```

## Multiple keys with Gitlab

Setting up multiple keys to work with gitlab can be tricky. You need to properly set your ssh configuration file, and then know how to properly clone the repository with the new settings. First generate keys you need:

```bash
ssh-keygen -t rsa -C "my.email@example.com" -b 4096 -f ~/.ssh/id_rsa.work
ssh-keygen -t rsa -C "my.email@example.com" -b 4096 -f ~/.ssh/id_rsa.home
```

Then, edit your `~/.ssh/config`:

```
Host work.gitlab.com
HostName gitlab.com
RSAAuthentication yes
User git
IdentityFile /home/user/.ssh/id_rsa.work

Host home.gitlab.com
HostName gitlab.com
RSAAuthentication yes
User git
IdentityFile /home/user/.ssh/id_rsa.home
```

Don't forget that `HostName` specifies the real host name to log into. Now when you want to clone the repo use the `Host` parameter from the configuration file e.g.

```bash
git clone git@work.gitlab.com:group/project.git
```

**None:** when using Ansible to clone the repo use the usual SSH pattern e.g. `git@gitlab.com:group/project.git`.

## Too many authentication failures?

You may see something like this from time to time:

```bash
user:~$ ssh root@192.168.0.165
Received disconnect from 192.168.0.165 port 22:2: Too many authentication failures
Connection to 192.168.0.165 closed by remote host.
```

If you want to see the real cause, use the debug flag when connecting e.g. `ssh root@192.168.0.165 -vv`. The most probable cuse that your client is offering too many keys to the SSH server, and all of them are incorrect, resulting in too many failed authentication attempts. This becomes a problem when you have too many keys on your machine (it seems that more than 5 is the [problem][3][^5][^6]). There are multiple solutions to this problem, depending what you want to achieve. You can delete all your keys from the SSH agent using `ssh-add -D`. If you're connecting for the first time to the server, you cannot copy the keys to it without being able to access it. Therefore, you must temporarily disable the public key authentication:

```bash
ssh -o PubkeyAuthentication=no root@192.168.0.165
```

Alternatively, see if you have some old unnecessary keys, and delete them.

[^1]: <https://web.archive.org/web/20180601095712/https://serverfault.com/questions/221760/multiple-public-keys-for-one-user>
[^2]: <http://archive.is/ahKSZ>
[^3]: <https://web.archive.org/web/20180601095448/https://askubuntu.com/questions/127369/how-to-prevent-write-failed-broken-pipe-on-ssh-connection>
[^4]: <http://archive.is/lblk0>
[^5]: <https://web.archive.org/web/20180919085737/https://serverfault.com/questions/580753/ssh-aborts-with-too-many-authentication-failures>
[^6]: <http://archive.li/5DZLa>

[1]: https://serverfault.com/questions/221760/multiple-public-keys-for-one-user
[2]: https://askubuntu.com/questions/127369/how-to-prevent-write-failed-broken-pipe-on-ssh-connection
[3]: https://serverfault.com/questions/580753/ssh-aborts-with-too-many-authentication-failures
