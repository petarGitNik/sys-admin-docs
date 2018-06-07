# Backups

There are number of ways in which you can perform backups:

* With scripts
* By built-in backup feature of the software used
* Using specialized backup programs (one can differentiate between small backup utilities, programs for home usage or small network usage, or enterprise scalable backup solutions)

Furthermore one can make difference between solutions that archive data in an open source or proprietary format. For example, using `tar` archive for a backup will create you no problems with interoperability. But if you want to use solution like `Bacula`, which uses its own image format, then you'll have to be aware that the image format which `Bacula` uses can only be read by `Bacula` itself. If you want to switch to another backup system, you may end up obligated to maintain the old one for legacy reasons.

If you're new to administration it may be hard to decide which approach you want to take. Whatever approach you choose remember two things:

1. RAID is **not** a backup
2. Redundancy **is** good
3. **Test your backups regularly** (make sure your backups actually work)

It is often recommeded that one backup system (no matter which approach you've chose) should follow **3-2-1 backup rule**:

1. You should have at least 3 copies of your data
2. Copies should be stored on at least two different media (i.e. devices)
3. One backup copy should be offsite

## General """recommendations"""

* Always try to find and use existing solutions (this will save you both development time and sanity, you don't want to chase bugs in you backup scripts)
* For some use cases (small number of servers) custom backup scripts are OK - it doesn't pay off much having to learn full fledged enterprise backup system just to backup couple of servers
* When writing scripts try to use tool such as `duplicity` rather than having to implement your own backup rotation
* Discuss backup interval with upper management - it should be their decision on how long backups should be retained and how often should backup be done - of course you have to give input and explain full vs incremental backups
* If you encrypt your backups, don't lose the key - you should have backup of your key
* Do not forget to backup your work PC/laptop also - a script or `duplicity` + CRON job would suffice for this use case
* If you decide to use random script from the Internet (I hope you don't) then read through it and make sure you understand it

## OK, I don't want to write scripts, what are my options?

Depending on your skills and the size of the servers you are taking care of I would recommend:

* [Bacula][1]
* [backuppc][2]

If you have a small network use `backuppc`. It's fairly easy to install on Ubuntu (guides on the Internet are outdated, but mostly relevant). If you have a large network, and you're fairly skilled (or have enough time and brain to learn fast) use `Bacula`. `Bacula` is little harder to wrap your head around it, but it supports all kinds of backup media (even tapes).

`Bacula` uses its own image format for backups, while `backuppc` uses simple tar archives. `backuppc` uses `rsync` over `ssh`.

## I want to write scripts, what are my options?

* `rsync` + `ssh`
* `tar` + `scp` or `rsync`
* `duplicity`
* `borg`
* `s3cmd` + Amazon S3 or DigitalOcean Spaces

Of all the options listed above, I'd use either `rsync`, `duplicity` or `s3cmd`. The "lowest" level of all of these is `rsync` (well, `tar` actually, but I'm not even considering it). `duplicity` has many backends, one of which is `rsync`. But it can also backup to google drive, dropbox, and many more. If you want to use [object storage][3][^1][^2] you can use `s3cmd`. But, keep in mind that `duplicity` can also backup to object storages such as Amazon S3 and DigitalOcean Spaces. The advantage of `duplicity` over `rsync` and `s3cmd` is that it can easily do incremental backups, it can be used to easily control for how long backups should be kept, and it can use GPG encryption to protect backups.

[^1]: <https://web.archive.org/web/20180607074833/https://www.digitalocean.com/community/tutorials/object-storage-vs-block-storage-services>
[^2]: <http://archive.is/1dpPr>

[1]: https://blog.bacula.org/
[2]: http://backuppc.sourceforge.net/
[3]: https://www.digitalocean.com/community/tutorials/object-storage-vs-block-storage-services
