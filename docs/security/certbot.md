# SSL/TLS

You should secure each site you deploy with SSL/TLS. There are various ways of doing it, but this guide will cover only encryption with _Let's Encrypt_.

## Enter `certbot`

Certbot is a utility that you can use through terminal to generate a certificate for your website. This guide won't cover installation though. Visit the [official website][1] for the installation instructions. After you choose the type of server you're using, and the correct version of your operating system (**Ubuntu 16.04**) then you can either:

1. Follow the official instructions
2. Disregard official instructions and get things done the right way

## What should I do next?

You can do the automated certification. But they mess with you apache configuration files. If you don't mind third party software editing your files, than take advantage. But this guide will take the second option of editing the apache files by hand.

## The safe and boring way

Luckily the `certbot` has a `--dry-run` options, so make use of it. If you have only one domain to register, test using this command:

!!! note
    You can probably drop the `--installer apache` option. Since the `certonly` will only generate a certificate. `--installer apache` is used to automatically configure the apache files[^1][^2].

```bash
sudo certbot certonly --authenticator webroot --installer apache -w /var/www/example.com -d example.com --dry-run
```

Drop the `--dry-run` flag if everything goes well, and there you have it. A certificate.

If you have multiple subdomains in the same document root, use the following:

```bash
sudo certbot certonly --authenticator webroot --installer apache -w /var/www/example.com -d example.com -d asterisk.example.com --dry-run
```

If you have two sites with same domain, but in two different roots, **do not** issues certificates separately for them! You will get a **certificate mismatch error**. Instead, use the same certificate, but define two different webroots:

```bash
sudo certbot certonly --authenticator webroot --installer apache -w /var/www/example.com -d site1.example.com -d site2.example.com -w /var/www/site3.com -d site3.example.com --dry-run
```

## What if I already have a certificate and want to add another subdomain?

Use the `--expand` flag. Following the last example, let's say you have to add SSL for another subdomain (e.g. `site3.example.com`), but which is located in different webroot:

```bash
sudo certbot certonly --authenticator webroot --installer apache -w /var/www/example.com -d site1.example.com -d site2.example.com -w /var/www/site3.com -d site3.example.com --expand --dry-run
```

## I've made a mistake, should I revoke my certificate?

To quote a user from _Let's Encrypt_ community [forum][2]:

> …and the answer to “should I revoke” is pretty much always “no.”

Either delete it, or leave it be until expires from the old age. Why? To quote official [documentation][3]:

> When a certificate’s corresponding private key is no longer safe, you should revoke the certificate.

If you need to delete, you can list all available certificates with:

```bash
sudo certbot certificates
```

And then delete a certificate as:

```bash
sudo certbot delete --cert-name <cert>
```

<!-- ## Automating with CRON -->

[^1]: <https://web.archive.org/web/20180402102235/http://manpages.ubuntu.com/manpages/artful/man1/certbot.1.html>
[^2]: <http://archive.is/oe1Dc>
[^3]: <https://web.archive.org/web/20180402104223/https://letsencrypt.org/docs/revoking/>
[^4]: <http://archive.is/H8xHO>

[1]: https://certbot.eff.org/
[2]: https://community.letsencrypt.org/t/certificate-name-mismatch-should-i-revoke-new-certificate/58047/7
[3]: https://letsencrypt.org/docs/revoking/
