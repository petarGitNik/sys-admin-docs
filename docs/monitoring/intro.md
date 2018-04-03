# Introduction to Prometheus

Prometheus is a monitoring software for servers (it can be used on PC too). Prometheus is not a monolithic application, but rather a collection of applications that expose data for Prometheus to scrape them. Prometheus by itself gives lots of stats, but you usually use more than just Prometheus to get more data.

For example, you have various applications called _exporters_ that expose various data from the system that Prometheus can scrape:

* Node exporter: exposes hardware an OS metrics
* Apache exporter: scrapes data from Apache server
* Mysqld exporter: scrapes data from MySQL rdbms
* PostgreSQL exporter: scrapes data from PostgreSQL rdbms

There are two more important components:

* Alertmanager: application for alerting
* Grafana: gives you a visual representation of scraped data

Hypothetically, you do not need Grafana. You can install some other GUI, or even use the built-in expression browser. If you want to use expression browser, then you're either use it through port forwarding with SSH, or putting it behind reverse proxy with basic authentication. My advice is to use the reverse proxy, because port forwarding seems to be a greater security risk.

## Architecture

Since Prometheus is modular, you do not need to install all components on all servers. For example, if a server does not have a MySQL database, you do not really need to install Mysqld exporter.

Furthermore, you do not need to have Prometheus instance on each server. You can have for example two Prometheus instances scraping data from dozens of servers. Why two? Redundancy. Similar applies to alertmanager.

## General installation notes

There are some general patterns that you'll follow when setting up the application and all its components. But, keep in mind that some of the exporters have their own quirks.

In general you will create a system user that will run the application, set the tool's unit file for `systemd`, and write its configuration file if needed.

This guide will cover:

* [x] Prometheus installation
* [x] Node exporter
* [x] PostgreSQL exporter
* [x] Mysqld exporter
* [ ] Apache exporter
* [x] Alertmanager
* [x] Writing rules
* [ ] Exporters behind a reverse proxy

Installation of Grafana will not be covered here. If you want to install it, follow the official installation manual.

!!! warning
    All configuration files provided here are just examples. They are not and do not aim to be in any shape or form production ready.

!!! warning
    Version numbers of tools and installation may change in the future. This manual does not aim to be up to date. If you want scripts that are more up to date, visit [this][2] repository.

## Is there an automatized way of doing this?

Yes. Google them. But as with every script, you have to read them to make sure they are safe in terms of security. I also have one installation script that I made myself, but at this stage it's really embarrassing. You can find it [here][2].

## Useful links

* [Wikitech article on Prometheus][1][^1][^2]

[^1]: <https://web.archive.org/web/20180401175122/https://wikitech.wikimedia.org/wiki/Prometheus>
[^2]: <http://archive.is/vgZa0>

[1]: https://wikitech.wikimedia.org/wiki/Prometheus
[2]: https://github.com/petarGitNik/prometheus-install
