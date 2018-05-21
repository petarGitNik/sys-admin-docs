# `git`

Use version control as much as possible.

## Make new branch

Make new branch and switch to it:

```bash
git checkout -b <branch>
```

## Delete branch

Delete branch locally:

```bash
git branch -d <branch>
```

Delete branch remotely:

```bash
git push <remote> --delete <branch>
```

## Merging a branch into master

If you want merge a branch into master, first make sure that branch is up-to-date with master (i.e. that there are no conflicts) and then checkout to master branch and merge the development branch[^1][^2][^3]:

```bash
git checkout development
git merge master
git checkout master
git merge development
```

[^1]: <https://stackoverflow.com/questions/14168677/merge-development-branch-with-master>
[^2]: <https://web.archive.org/web/20180521113251/https://stackoverflow.com/questions/14168677/merge-development-branch-with-master>
[^3]: <http://archive.is/gRPJl>
