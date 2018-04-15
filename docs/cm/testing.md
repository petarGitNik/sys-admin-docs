# Testing With Molecule and Docker

Molecule is a testing framework for Ansible roles. As you develop your basic playbooks, and they grow, you will notice that it will not be feasible to run your whole playbook time and time again, just to test one new task. Also, booting virtual machine through Vagrant is slow, and it can take a lot of time. Docker can certanly speed things up.

You can also speed things up by testing roles instead of whole playbooks. `Molecule` does just that.

However, there are many serious limitations to docker. It does not natively supports `systemd` (and I use it for `Prometheus` and its components). You may overcome this with docker images that provide  `systemd` support.

## Docker

Before you start using `Molecule` you must install docker. This guide will not cover tutorial on how to install Docker. Follow the official [how to][1]. You also **must** have an account on Docker hub.

See these tutorials in order to learn basic commands and some vary basic workflow with Docker:

* [Docker Tutorial - What is Docker & Docker Containers, Images, etc?][2]
* [Docker Tutorial - Docker Container Tutorial for Beginners][3]
* [Docker Container Tutorial - How to build a Docker Container & Image][4]

## Molecule

Before you start testing make sure you are logged in to docker through CLI. To do that type `docker login` in terminal and follow instructions. When you're done with that you may use Molecule.

Downside to Molecule is that for the time being it can only be used with `pip`, `virtualenv`, and Python `2.X`. So no `pipenv` magic here (_sigh_).

Make project directory and activate virtualenv:

```bash
mkdir ansible-test-dir
cd ansible-test-dir
python -m virtualenv venv
source venv/bin/activate
```

Install `Molecule` and relevant packages:

```bash
pip install molecule docker-py
pip freeze > requirements.yml
```

You can either initialize new role:

```bash
molecule init role --rolename <my_role> --driver-name docker
```

or add tests to an existing one:

```bash
molecule init scenario --role-name <my_existing_role> --scenario-name default
```

To test whether the `Molecule` is working properly cd into the role directory and run tests:

```bash
cd <my_role>
molecule --debug test
```

You can drop the `--debug` flag if you want.

[1]: https://docs.docker.com/install/linux/docker-ce/ubuntu/#os-requirements
[2]: https://www.youtube.com/watch?v=pGYAg7TMmp0
[3]: https://www.youtube.com/watch?v=JBtWxj9l7zM
[4]: https://www.youtube.com/watch?v=K6WER0oI-qs
