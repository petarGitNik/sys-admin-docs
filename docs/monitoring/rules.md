# Writing rules

This tutorial is written for Prometheus 2.X. Earlier versions of Prometheus had a different style of writing the rules. Rules in Prometheus 2.X are written in [YAML][1][^1][^2] format.

First thing you should know is that there are two types of rules in Prometheus:

* [Recoding rules][2][^3][^4]
* [Alerting rules][3][^5][^6]

Rules are evaluated at regular intervals, and they can be included in `prometheus.yml` configuration file with the following line:

```yaml
rule_files:
  - 'prometheus.rules.yml'
```

You can create `prometheus.rules.yml` file in the same directory where `prometheus.yml` is stored, e.g. `/etc/prometheus/prometheus.rules.yml`.

## Recording rules

If you have a complex query that you use very often, you can make it faster by writing a recording rule.

The rules file contains the primary tag named `groups`. `group` contains both recording and alerting rules. You differentiate between different groups with the `name` tag. Here is a simple example:

```yaml
groups:
  - name: recording_rules
    rules:
      - record: node_exporter:node_memory_free:memory_used_percents
        expr: 100 - 100 * (node_memory_MemFree / node_memory_MemTotal)
```

In this example, I have marked recording rules as `recording_rules` in the `name` tag. The rules themselves are located within `rules` tag. A rule is defined with minimum two tags: `record` and `expr`. The `expr` is the expression you want to evaluate at regular intervals. The `record` tag will be the name of your time series. You can use the title from the `record` rule in your Grafana dashboard or execute it in Prometheus's built-in expression browser.

What does this particular rule do? It uses Node Exporter to calculate the percent of memory used. How to name rules? There is no mandatory way in which you must name the rules. In this example I opted for the approach `exporter:metrics_used:unit` i.e. `node_exporter:node_memory_free:memory_used_percents`. But take into consideration that this is against the best practices. The official Prometheus documentation recommends to use `level:metric:operations`. For more detail see the [docs][4][^7][^8].

In addition to these labels, you can add interval at which rules will be evaluated (if you want to override global configuration), and you can add labels. Here is the full example, with one new rule added.

<!-- you have to change and optimize these rules -->

```yaml
groups:
  - name: recording_rules
    interval: 5s
    rules:
      - record: node_exporter:node_filesystem_free:fs_used_percents
        expr: 100 - 100 * ( node_filesystem_free{mountpoint="/"} / node_filesystem_size{mountpoint="/"} )

      - record: node_exporter:node_memory_free:memory_used_percents
        expr: 100 - 100 * (node_memory_MemFree / node_memory_MemTotal)
```

## Alerting rules

Alerting rules are more tricky to write. But not too hard. They are very similar to recording rules, but with couple of additional tags. Let's say you are interested in load average over the last 15 minutes. You can query that with Prometheus, if you have node exporter installed, by typing `node_load15` in the expression browser. If the load average over the last 15 minutes is very high, that means that your server is heavily utilized, and maybe you'd want to be notified about that. Enter alertmanager.

```yaml
groups:
  - name: alerting_rules
  rules:
    - alert: LoadAverage15m
      expr: node_load15 >= 0.75
      labels:
        severity: major
      annotations:
        summary: "Instance {{ $labels.instance }} - high load average"
        description: "{{ $labels.instance  }} (measured by {{ $labels.job }}) has high load average ({{ $value }}) over 15 minutes."
```

First things first. If you have 1 core on your CPU, your max load average will be 1. In this case, we want alert when 75% of the cpu is used (i.e. `expr: node_load15 >= 0.75`). All up to the `expr` tag is same or analogous to recording rules. We have some additional tags here: `annotation`, `summary`, and `description`. When the alert is firing, and you get the mail, these are the tags that will be used to form the mail. Essentially, this is a very basic mail template.

The `$labels` variable holds the label key/value pair. The `$value` variable holds, you guessed it, a value of a given instance. In this case, `$labels.instance` will display to you which server instance has reached the threshold (e.g. `localhost:9100`). The `$labels.job` will display the exporter that is reporting this value. In this case, this is the `node_exporter`. The value will be the `$value` of the measured parameter, for example `0.83`.

Of course, you can use your recorded rules in combination with alert rules. Here is a full example of a rule file:

```yaml
groups:
  - name: recording_rules
    interval: 5s
    rules:
      - record: node_exporter:node_filesystem_free:fs_used_percents
        expr: 100 - 100 * ( node_filesystem_free{mountpoint="/"} / node_filesystem_size{mountpoint="/"} )

      - record: node_exporter:node_memory_free:memory_used_percents
        expr: 100 - 100 * (node_memory_MemFree / node_memory_MemTotal)

  - name: alerting_rules
    rules:
      - alert: LoadAverage15m
        expr: node_load15 >= 0.75
        labels:
          severity: major
        annotations:
          summary: "Instance {{ $labels.instance }} - high load average"
          description: "{{ $labels.instance  }} (measured by {{ $labels.job }}) has high load average ({{ $value }}) over 15 minutes."

      - alert: MemoryFree10%
        expr: node_exporter:node_memory_free:memory_used_percents >= 90
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Instance {{ $labels.instance }} hight memory usage"
          description: "{{ $labels.instance }} has more than 90% of its memory used."

      - alert: DiskSpace10%Free
        expr: node_exporter:node_filesystem_free:fs_used_percents >= 90
        labels:
          severity: moderate
        annotations:
          summary: "Instance {{ $labels.instance }} is low on disk space"
          description: "{{ $labels.instance }} has only {{ $value }}% free."
```

With these several rules you can know if your server's CPU, memory, or disk are heavily used/full. Pretty neat.

## Check rules file

Prometheus includes a useful utility that you can use to check whether the rules you've written are OK. You can use it like this:

```bash
promtool check rules /etc/prometheus/prometheus.rules.yml
```

If you want to know more about the prometheus rules, check out the official documentation.

[^1]: <https://web.archive.org/web/20180402105628/https://en.wikipedia.org/wiki/YAML>
[^2]: <http://archive.is/43ktY>
[^3]: <https://web.archive.org/web/20180402110048/https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/>
[^4]: <http://archive.is/1jTjb>
[^5]: <https://web.archive.org/web/20180402110159/https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/>
[^6]: <http://archive.li/mtQBp>
[^7]: <https://web.archive.org/web/20180402111821/https://prometheus.io/docs/practices/rules/>
[^8]: <http://archive.is/m1LNV>

[1]: https://en.wikipedia.org/wiki/YAML
[2]: https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/
[3]: https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/
[4]: https://prometheus.io/docs/practices/rules/
