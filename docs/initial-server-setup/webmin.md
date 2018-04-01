# How To Install Webmin

This guide is based on [How To Install Webmin on Ubuntu 16.04][1][^1][^2] by DigitalOcean.

## Installation

Add a Webmin repository to your sources list:

```bash
sudo vi /etc/apt/sources.list
```

Add the following:

```ini
deb http://download.webmin.com/download/repository sarge contrib
```

Then add the Webmin PGP key, and install it:

```bash
wget http://www.webmin.com/jcameron-key.asc
sudo apt-key add jcameron-key.asc
sudo apt-get update
sudo apt-get install webmin
```

Don't forget to change the webmin default port, and to allow it on firewall. Follow [this][2][^3][^4] guide to change the port.

## SSL and reverse proxy

Adding encryption to your Webmin is essential, but it may not be as straight forward. The easiest way to encrypt the Webmin is using Let's Encrypt. Let's Encrypt puts a text document under your webroot in a hidden directory, and then tests whether it can access it. This can prove to be a problem if you have multisite server setup, and you don't have globally configured `ServerName` directive for Apache server. Even then, you may have it configured, but to something like generic server hosting domain name like: `provider1234.example.com`. Again, it may prove impossible to encrypt it like that since the default FQDN in `ServerName` may not have default web document root. So that's one of the reasons I recommend putting Webmin through the reverse proxy, since that way you can just use a certificate of an existing site to encrypt your control panel. Of course, your `ServerName` directive may contain the legitimate server domain name that has a nice web root directory. In that case, you don't have any problem doing things like explained in Webmin documentation (and the DigitalOcean tutorial provided here).

But the second reason I like the reverse proxy is that I don't have to poke another hole in firewall. By allowing another port, bots and hackers may do a scan and discover easily another way in. Especially if you keep Webmin's default port `10000`. You're basically letting the whole world know which control panel you're using. With reverse proxy you may _"append"_ it to the root URL as `webmin/` or `my supersecretcontrolpanel/` i.e. `https://example.com/webmin/`. If you want to be extra sneaky you can add some basic authentication for reverse proxy. That could serve as another layer of protection.

## Webmin behind a reverse proxy

!!! note
    If you want this to work, you have to have your Apache configuration file setup for your website, with the defined `DocumentRoot` directive. That also implies that you have made appropriate directory on your server to match the path in the `DocumentRoot` directive.

[not finished]

[^1]: <https://web.archive.org/web/20180401081521/https://www.digitalocean.com/community/tutorials/how-to-install-webmin-on-ubuntu-16-04>
[^2]: <http://archive.is/I8YKg>
[^3]: <https://web.archive.org/web/20180401082215/https://doxfer.webmin.com/Webmin/Webmin_Configuration>
[^4]: <http://archive.is/NKPE1>


[1]: https://www.digitalocean.com/community/tutorials/how-to-install-webmin-on-ubuntu-16-04
[2]: https://doxfer.webmin.com/Webmin/Webmin_Configuration#Ports_and_Addresses
