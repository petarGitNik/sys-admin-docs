# Sys Admin Docs

This repository contains various documentation pertaining to system administration. It is not in any way meant to be authoritative nor it claims to be an expert level docs. In essence these are tips, tricks, and tutorials I have compiled over a time while working as a sys admin.

## How to use this repo?

The docs are powered by [MkDocs](http://www.mkdocs.org/). The code is managed with git and [pipenv](https://docs.pipenv.org/). When you clone the repo, cd into the directory and do `pipenv install`. When you install packages, drop into the virtualenv with `pipenv shell` and start a local server to see the docs with `mkdocs serve`.

```bash
pip3 install pipenv
git clone https://gitlab.com/petarnikolovski/sys-admin-docs.git
cd sys-admin-docs
pipenv install
pipenv run mkdocs serve
```
