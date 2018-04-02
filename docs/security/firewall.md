# Managing `ufw`

Basics of installing and enabling `ufw` were covered in the _Initial Server Setup_ chapter. They will not be repeated here. This chapter will contains sume tips and tricks.

## Allow connections

```bash
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
```

If you want to allow only specific IP on one port use:

```bash
sudo ufw allow from <IP> to any port <PORT>
```

## List applications

```bash
sudo ufw app list
sudo ufw app info "Apache Full"
sudo ufw allow in "Apache Full"
```

## Checking status

```bash
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered
```

## Deleting a rule

```bash
sudo ufw status numbered
sudo ufw delete <number>
sudo ufw delete allow ssh
sudo ufw delete deny 80
```

## Deny connections

!!! note
    `deny` block a port.

```bash
sudo ufw deny http
sudo ufw deny <port>
```

Use case: maybe you have a SMTP mail gone rogue, and you want to block outgoing emails. You can do this with `deny`:

```bash
sudo ufw deny out 25
```

## Enable, disable, and reset

!!! note
    `reset` sets your firewall rules back to defaults.

```bash
sudo ufw enable
sudo ufw disable
sudo ufw reset
```

## Resources

* [Configure Ubuntu Firewall (UFW) on Ubuntu 14.04][1][^1][^2]
* [Getting Started with UFW (Uncomplicated Firewall) on Ubuntu 15.04][2][^3][^4]
* [How To Set Up a Firewall with UFW on Ubuntu 16.04][3][^5][^6]
* [UFW Essentials: Common Firewall Rules and Commands ][4][^7][^8]

[^1]: <https://web.archive.org/web/20180401102927/https://www.vultr.com/docs/how-to-configure-ufw-firewall-on-ubuntu-14-04>
[^2]: <http://archive.is/QpuOa>
[^3]: <https://web.archive.org/web/20180401103112/https://www.howtoforge.com/tutorial/ufw-uncomplicated-firewall-on-ubuntu-15-04/>
[^4]: <http://archive.is/XH7S1>
[^5]: <https://web.archive.org/web/20180401103223/https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-16-04>
[^6]: <http://archive.is/cB665>
[^7]: <https://web.archive.org/web/20180401104142/https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands>
[^8]: <http://archive.is/klW3V>

[1]: https://www.vultr.com/docs/how-to-configure-ufw-firewall-on-ubuntu-14-04
[2]: https://www.howtoforge.com/tutorial/ufw-uncomplicated-firewall-on-ubuntu-15-04/
[3]: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-16-04
[4]: https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands
