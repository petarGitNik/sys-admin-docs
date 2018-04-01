# Intrusion Prevention: `fail2ban`

The moment your server is up, bots will try to crack down on it. You need an intrusion prevention software to protect your server from brute-force attacks. Enter fail2ban.

Fail2ban monitors your log files for suspicious activities, and bans IP addresses based on them.

## Sources

This is compilation of several tutorials. Namely:

* [How To Protect SSH and Apache Using Fail2Ban on Ubuntu Linux][1][^1][^2]
* [Simple fail2ban DOS jail][2][^3][^4]
* [How to set up fail2ban to read multi log in a jail?][3][^5][^6]
* [Fail2Ban setup][4][^7][^8]
* [How to Secure SSH server from Brute-Force and DDOS with Fail2ban (Ubuntu)][5][^9][^10]

## How do I configure it on Ubuntu?

If it's the fresh server installation start with:

```bash
sudo apt-get update
sudo apt-get dist-upgrade
sudo apt-get install fail2ban
```

Then copy, paste, edit, and modify the following files:

```bash
sudo vi /etc/fail2ban/jail.local
```

```ini
# Block login attmepts
[apache]
enabled = true
port = http,https
filter = apache-auth
logpath = /var/log/apache2/*error.log
          /var/log/apache2/*errors.log
maxretry = 3
bantime = 600


# Block the remote host that is trying to request suspicious URLs
[apache-overflows]
enabled = true
port = http,https
filter = apache-overflows
logpath = /var/log/apache2/*error.log
          /var/log/apache2/*errors.log
maxretry = 3
bantime = 600


# Ban the remote host that is trying to search for scripts on the website
# to execute
[apache-noscript]
enabled = true
port = http,https
filter = apache-noscript
logpath = /var/log/apache2/*error.log
          /var/log/apache2/*errors.log
maxretry = 3
bantime = 600


# Block the remote host that is trying to request malicious bot
[apache-badbots]
enabled = true
port = http,https
filter = apache-badbots
logpath = /var/log/apache2/*error.log
          /var/log/apache2/*errors.log
maxretry = 3
bantime = 600


# Block DOS attacks over GET
[http-get-dos]
enabled = true
port = http,https
filter = http-get-dos
logpath = /var/log/apache2/*access.log
maxRetry = 100
findtime = 300
bantime = 6000


# Block DOS attacks over POST
[http-post-dos]
enabled = true
port = http,https
filter = http-post-dos
logpath = /var/log/apache2/*access.log
maxRetry = 60
findtime = 300
bantime = 6000


# Block the failed login attempts to SSH server
[ssh]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 600


# Block DDOS on ssh
[ssh-ddos]
enabled = true
port = ssh,sftp
filter = sshd-ddos
logpath = /var/log/auth.log
maxretry = 2
bantime = 600

# Webmin
[webmin-auth]
enabled = true
port    = 10000
logpath = %(syslog_authpriv)s
maxretry = 3
bantime = 600
```

[Source][2][^3][^4] for the DOS jail below.

```bash
sudo vi /etc/fail2ban/filter.d/http-get-dos.conf
```

```ini
# Fail2Ban configuration file
#
# NOTE
# You should set up in the jail.conf file, the maxretry and findtime carefully in order to avoid false positives.
#
# Author: http://www.go2linux.org
# Modified by: samnicholls.net
#    * Mon 6 Jun 2016 - Updated failregex to capture HOST group correctly

[Definition]

# Option: failregex
# NOTE: The failregex assumes a particular vhost LogFormat:
#           LogFormat "%t [%v:%p] [client %h] \"%r\" %>s %b \"%{User-Agent}i\""
#       This is more in-keeping with the error log parser that contains an explicit [client xxx.xxx.xxx.xxx]
#       but you could obviously alter this to match your own (or the default LogFormat)
failregex = \[[^]]+\] \[.*\] \[client <HOST>\] "GET .*

# Option: ignoreregex
# Notes.: regex to ignore. If this regex matches, the line is ignored.
# Values: TEXT
ignoreregex =
```

[Source][2][^3][^4] for the DOS jail below.

```bash
sudo vi /etc/fail2ban/filter.d/http-post-dos.conf
```

```ini

# Fail2Ban configuration file
#
# NOTE
# You should set up in the jail.conf file, the maxretry and findtime carefully in order to avoid false positives.
#
# Author: http://www.go2linux.org
# Modified by: samnicholls.net
#    * Mon 6 Jun 2016 - Updated failregex to capture HOST group correctly

[Definition]

# Option: failregex
# NOTE: The failregex assumes a particular vhost LogFormat:
#           LogFormat "%t [%v:%p] [client %h] \"%r\" %>s %b \"%{User-Agent}i\""
#       This is more in-keeping with the error log parser that contains an explicit [client xxx.xxx.xxx.xxx]
#       but you could obviously alter this to match your own (or the default LogFormat)
failregex = \[[^]]+\] \[.*\] \[client <HOST>\] "POST .*

# Option: ignoreregex
# Notes.: regex to ignore. If this regex matches, the line is ignored.
# Values: TEXT
ignoreregex =
```

Use these to check if everything is all right (restart if needed):

```bash
sudo systemctl restart fail2ban
sudo fail2ban-client status
```

Check `iptables` with:

```bash
sudo iptables -S
sudo iptables -L
```

## Additional info

You should be careful about perma-bans. Some attacks will be from dynamic IP addresses, and those may later be assigned to legit users. If, however, you want to permanently ban certain attacks, you can set ban time to negative value.

!!! warning
    Another reason to be careful about aggressive banning is that you may fail typing your password several times at SSH login. You can, and will, end up being banned on your own server. Count on it.

!!! info
    When you reset `fail2ban` daemon all bans are gone.

## Tips and tricks

Check/count how many IPs appeared in `fail2ban` log file:

```bash
sudo awk '{ print $8 }' /var/log/fail2ban.log | sort | uniq -d | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" | wc -l
```

Or of you want to know which ones/how many were banned:

```bash
sudo awk '$7 == "Ban" { print $8 }' /var/log/fail2ban.log | sort | uniq -d | grep -oE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" | wc -l
```

[^1]: <https://web.archive.org/web/20180401091543/https://blog.rapid7.com/2017/02/13/how-to-protect-ssh-and-apache-using-fail2ban-on-ubuntu-linux/>
[^2]: <http://archive.is/J6llF>
[^3]: <https://web.archive.org/web/20180401091733/https://gist.github.com/SamStudio8/92507ad3e317edb9b869c20bb2623fcf>
[^4]: <http://archive.is/NeknK>
[^5]: <https://web.archive.org/web/20180401091818/https://serverfault.com/questions/486301/how-to-set-up-fail2ban-to-read-multi-log-in-a-jail>
[^6]: <http://archive.li/sOi1R>
[^7]: <https://web.archive.org/web/20180401091919/https://www.virtualmin.com/node/52599>
[^8]: <http://archive.li/i7CRg>
[^9]: <https://web.archive.org/web/20180401092047/https://blog.mypapit.net/2011/07/how-to-secure-ssh-server-from-brute-force-and-ddos-with-fail2ban-ubuntu.html>
[^10]: <http://archive.li/XEWuG>

[1]: https://blog.rapid7.com/2017/02/13/how-to-protect-ssh-and-apache-using-fail2ban-on-ubuntu-linux/
[2]: https://gist.github.com/SamStudio8/92507ad3e317edb9b869c20bb2623fcf
[3]: https://serverfault.com/questions/486301/how-to-set-up-fail2ban-to-read-multi-log-in-a-jail
[4]: https://www.virtualmin.com/node/52599
[5]: https://blog.mypapit.net/2011/07/how-to-secure-ssh-server-from-brute-force-and-ddos-with-fail2ban-ubuntu.html
