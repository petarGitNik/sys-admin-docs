# Daemons

## Dealing with daemons

Find a process:

```bash
ps -e | grep <process>
ps aux | grep <process>
```

Kill a process:

```bash
pkill -o -u $USER <process>
```

List a long format table for a process:

```bash
ps -lfC <process>
```

List a long format table for a process owned by a user:

```bash
ps -lf -U <user> <process>
```
