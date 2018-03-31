# Initial Firewall Configuration

First thing you need to do when you log into a fresh server is to check the firewall. Depending on the OS configuration by the server provider, Ubuntu may or may not come bundled with `ufw` (it should, but always check). In any case there are `iptables` which are interface to Linux firewall rules. I'd opt out for `ufw` however, since it's very simple to use. It's a win win since `ufw` uses `iptables` for configuration.

Check whether the firewall is active by typing:

```bash
sudo ufw status
```

On a fresh server it should return `Status: inactive`.

!!! warning
    Do not enable firewall at this point. Do not forget the following thing: YOU NEED TO ALLOW SSH CONNECTIONS BEFORE YOU ACTIVATE THE FIREWALL. Don't be stupid and lock yourself out from your own server.

These are the step you need to take:

1. Install the firewall
2. Set the rules (including the enabling of the SSH)
3. Activate the firewall
4. Test connections

## What ports should I pay attention to?

Pay attention to _standard_ port numbers for SSH, HTTP, and HTTPS. They are:

| Protocol | Standard Port |
| :------- | :-----------: |
| SSH | 22 |
| HTTP | 80 |
| HTTPS | 443 |

## How can I be sure that SSH is on port 22?

There are two ways, and I recommend checking both.

First one is checking `/etc/services` file. You can do this in the following manner:

```bash
grep -ni <pattern> /etc/services
```


Replace the pattern with `ssh`, `http`, `postgres`, and so on. The pattern is case insensitive.

The second ways is to check the ssh config file.

```bash
less /etc/ssh/ssh_config
```

If the protocols are associated with their standard ports, than you can use keywords instead of numbers to enable them on the `ufw`.

## Setup

Follow these steps to install, activate, and configure the firewall.

!!! warning
    Make sure that SSH is on the standard port.

```bash
sudo apt-get install ufw
sudo ufw status verbose
sudo nano /etc/default/ufw
```

In the config file, make sure that IPv6 is enabled, add or modify a file to contain:

```
IPV6=yes
```

After this, you can configure the firewall and activate it. Answer yes when you're prompted.

```bash
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```
