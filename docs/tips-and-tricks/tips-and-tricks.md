# Miscellaneous

List all system users:

```bash
awk -F: '{ print $1 }' /etc/passwd
```

List all system users, and find out where they live:

```bash
awk -F: '{ print $1" -> "$7 }' /etc/passwd
```

Check the location of the binary (use it to check whether something is installed):

```bash
which <package-name>
```

Search for a case insensitive pattern through all text files within a current directory and its subdirectories, and show me the line number in results:

```bash
grep -rni <pattern> *
```

List all lines except the lines containing the pattern:

```bash
grep -v <pattern>
```

Pretend you're another user:

```bash
sudo -u <user> <command>
```

Pretend you're another user, and also crash into their home:

```bash
sudo -H -u <user> <command>
```

Switch to another user:

```bash
sudo su - <user>
```

Copy over the interwebs[^4][^5][^6]:

```bash
scp <user>@<host>:path/to/file.txt /path/to/download/dir
```

How many cores do I have?

```bash
nproc
```

Start a test server when developing:

```bash
python3 -m http.server
```

Untar `.gz` archive into a specified directory:

```bash
tar xvzf archive.tar.gz -C /destination/directory
```

Make an archive:

```bash
tar czvf <NAME_OF_THE_ARCHIVE>.tar.gz <FILE_TO_BE_ARCHIVED>
```

Sometimes you need to grep inside `tar` file or or concatenate two (cat). You can't normally do that with `cat` and `grep`, instead use:

```bash
zgrep
zcat
```

Follow redirect when dowlonading:

```bash
curl -L <URL>
```

Print a specific line from a file:

```bash
sed -n "78p" ./text.txt
```

Check logins:

```bash
who
w
last
```

Print groups current user belongs to:

```bash
groups
```

List PPAs on the system:

```bash
apt-cache policy | grep http | awk '{ print $2 $3 }' | sort -u | grep launchpad
```

I forgot model of my laptop:

```bash
sudo lshw | grep product
```

List packages installed with `apt`:

```bash
apt list --installed
```

[^4]: <https://haydenjames.io/linux-securely-copy-files-using-scp/>
[^5]: <https://web.archive.org/web/20180402132849/https://haydenjames.io/linux-securely-copy-files-using-scp/>
[^6]: <http://archive.is/hBKUV>
