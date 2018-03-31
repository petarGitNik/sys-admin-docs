# Initial Server Setup Checklist

Not every initial server setup is the same, but they do have lots in common. The following is a simple checklist that you can use in general when setting up the server. Of course, list of programs needed depends on what kind of website/app you're deploying on your server.

In short this is how you want to do it:

1. SSH into the server
2. Configure firewall
3. _Optional:_ Install `nano` (or some other editor you like)
4. Make system users (at least one for yourself - and have some creative name), and set their privileges
5. Disable SSH login for root user, but enable SSH for at least one other user (for yourself as an admin)
6. _Optional:_ Install control panel (`webmin`, the hated)
7. Setup LAMP or LEMP (see notes below)
8. _Optional:_ Change `webmin` default port
9. _Optional:_ Change `webmin` outgoing mail configuration
10. _Optional:_ Configure monitoring with `webmin`
11. Install and configure `fail2ban`
12. Setup the desired website/app
13. Add SSL for the website
14. Setup and configure monitoring system

## Reasoning behind the checklist

### What editor should I use?

The editor you will use to edit the configuration files on the server can be really any editor you are comfortable with. That could be `vi`. In any case, if there are any other users using the server, assume that they are not in any level or form proficient in `vi` and install `nano`. `nano` is easy and friendly for beginners, and can be used by anyone almost without any kind of learning curve.

### But should I install additional themes for my `vi`?

That is up to you. If you want to add any kind of custom code on your server, you should go through that code and read it to make sure there are not malicious things in it. For that reason alone, I do not any theme to `vi`.

### Why should I use a web hosting control panel?

You don't have. And if you know your CLI well, monitoring system + CLI should cover all your needs 99% (if not 100%) of the time. Installing a web hosting control panel also lowers your security since it potentially opens a  new risky endpoints for security. For that reasons, you should change default webmin port from `10000` to some other ports, although, it may be best to put webmin behind a reverse proxy (and maybe even with basic authentication - double the passwords, double the security - or at least the time to breach your system).

### Why do you recommend Webmin (gross, puke)?

I know, I know. People hate webmin. But some other popular solutions are really horible. Take `Plesk` for example. I **hate** the damn thing. As one person described it[^1][^2][^3]:

> Plesk is like the borg, when installed on your server, all bits of that server become assimilated

Plesk messes with all your configuration files, rendering all standard places for configuration obsolete, and having its own logic and places to put all of those. This implicitly forces you to use Plesk for everything, from administration to setting up of websites. If you love and appreciate yourself, take an option that will give you the most control: **CLI**.

Since I'm a hater, I wouldn't be true to myself if I didn't recommend you [this][1] article[^4][^5].

**tl;dr** version: Use CLI alone or (in addition) if you want a web control panel that doesn't mess up with your configuration use webmin. Webmin is easy to learn even for beginers, and you have almost all the options that you need. At least, if you're managing small number of servers.

### LAMP or LEMP?

It depends on your business needs. Maybe neither. In any case, this manual assumes you're using Ubuntu as your operating system. The server that you'll use will depend on your skills, business needs, and website requirements. At this moment I'm using only apache, so this manual may end up being concentrated just on apache. As for the database, you may need MySQL or PostgreSQL, or even some other. Again, it depends on the type of website you're setting up. And last but not least, PHP. Totally optional, at least if you're not using website which requires it (like Moodle or Wordpress). Also, your server operating system can come preinstalled with LAMP and or additional web hosting control panel. This may shorten your setup time.

### What should I use for monitoring system?

Something that is scalable. It's hard to manage lots of instances of an app on many different servers. Whatever you choose to use, a good idea is to be scalable, that it can also monitor other servers than the server that is installed on, and something which setup you can fully automate. Prometheus monitoring system is a good application, but it has a long learning curve for beginners, and its documentation is also really terse, which makes it even more hard for beginners. 

[^1]: <https://community.phpfox.com/forum/thread/42388/is-plesk-any-good/>
[^2]: <https://web.archive.org/web/20180331185833/https://community.phpfox.com/forum/thread/42388/is-plesk-any-good/>
[^3]: <http://archive.is/cqort>
[^4]: <https://web.archive.org/web/20180331190411/http://www.anchor.com.au/hosting/development/we_hate_plesk_and_cpanel>
[^5]: <http://archive.is/Pa67R>

[1]: http://www.anchor.com.au/hosting/development/we_hate_plesk_and_cpanel
