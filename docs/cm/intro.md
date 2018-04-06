# Automation and Configuration Management

This section is in early development. At this point I will present you with the short guide how to setup environment in which you can test out Ansible.

In order to have a proper environment in which you can experiment and develop Ansible playbooks, you'll need:

* [Ansible][2]
* [Vagrant][1]
* [VirtualBox][3]
* Python 2.X or 3.X (this guide assumes you'll using Python 3.X)
* [Pipenv][4]

## Where do I start?

Before you start I want to explain my reasons for using `pipenv`. Pipenv is a new tool that brings together `pip` and `virtualenv`. Usually when I start a project I always limit its scope to virtualenv as much as I can. Since Ansible is built with Python, I use the virtual environment to contain it. In that way I do not have to install it globally on my machine. However, if you need it, you don't have to go through the hassle of installing `pipenv`.

### Pipenv

So, start first with the `pipenv`:

```bash
pip install --user pipenv
```

Use either `pip` or `pip3`. Also add a `--user` flag on Ubuntu if you have problems with permissions.

### Ansible

Now that you have `pipenv` you can proceed to install Ansible in virtual environment. Start by making a project directory:

```bash
mkdir ansible-testground
cd ansible-testground
pipenv --three
```

This will make a `Pipfile`, which will be used to track all the packages you'll install. This is also a good time to do a `git init`, and add your test project some version control.

Next, install the Ansible:

```bash
pipenv install ansible
```

Notice, with `pipenv` you're not using `pip` command to install packages. And you do not have to worry about `requirements.txt` since you have a `Pipfile`.

### Vagrant

Download the latest version from the official site, and install it using `gdebi`:

```bash
wget https://releases.hashicorp.com/vagrant/2.0.3/vagrant_2.0.3_x86_64.deb
sudo gdebi vagrant_2.0.3_x86_64.deb
```

### VirtualBox

Download the latest version from the official site, and install it using `gdebi`:

```bash
wget https://download.virtualbox.org/virtualbox/5.2.8/virtualbox-5.2_5.2.8-121009~Ubuntu~xenial_amd64.deb
sudo gdebi virtualbox-5.2_5.2.8-121009~Ubuntu~xenial_amd64.deb
```

## What next?

Well, that's it for a basic setup. Next you'll use Vagrant to make a virtual Ubuntu instance.

### Working with Vagrant

In your project folder initialize new virtual OS in VirtualBox using Vagrant:

```bash
vagrant init ubuntu/xenial64
vagrant up
```

`vagrant init` will set the proper image, and `vagrant up` starts the machine.

!!! info
    If you want to stop, destroy, and start fresh you can do this with: `vagrant halt && vagrant destroy && vagrant up`

!!! info
    You can use `vagrant status` to check the status of the machine, and `vagrant provision` when you need to provision your playbook. To access your virtual machine use `vagrant ssh`.

### Vagrantfile

Here is a basic configuration you'll need to get up and running with Ansible:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.network "private_network", ip: "192.168.33.20"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provisioning/playbook.yml"
    ansible.extra_vars = {
      ansible_python_interpreter: "/usr/bin/python3",
    }
  end
end
```

If you try to do `vagrant provision` now, you will fail, because you haven't yet made Ansible playbook, which you defined in `Vagrantfile`.

## The first playbook

Within your project directory execute the following:

```bash
mkdir provisioning/
touch provisioning/playbook.yml
```
And in the `playbook.yml` put the following content:

```yaml
---
- hosts: all
  tasks:
    - ping:
```

Now you can execute `vagrant provision`. That's it. You've successfully ran your first Ansible playbook.

[1]: https://www.vagrantup.com/
[2]: https://www.ansible.com/
[3]: https://www.virtualbox.org/
[4]: https://docs.pipenv.org/
