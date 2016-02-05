<!--
############################################################################### 
# Abstract:
# Mokafelk README
#
# Description:
# Dockerized mock Kafka and Elasticsearch-Logstash-Kibana servers for testing msgglass 
#
# Copyright (c) 2016 Lingxiao Xia [s1006595991 at gmail dot com]
# Project: mokafelk
# Creation: Lingxiao Xia
# Creation Date: 2016-02-03
################################################################################
--> 

# Ansible controlled Dockerized mock Kafka and Elasticsearch-Logstash-Kibana servers
It uses `ansible` to spin up a `kafka` cluster. It also spins up a whole `ELK` stack which consumes from the `kafka` containers so that you have near real-time view of your data. This project is designed for testing another github project named `msgglass`, it could be used for testing any `kafka` consumer agent in theory. No security measure is included to protect your data. Use inside an intranet or under safe conditions ONLY. DO NOT use in production.

This file uses some `jinja2` notations and the variables are specified in `config.yaml`.

## Pre-requisite 
This project uses `docker 1.10.0` or higher, `docker-compose 1.6.0` or higher and `ansible 2.0.0.2` or higher so please make sure you have them installed. Please also configure your `/etc/ansible/hosts` accordingly to include a host called `localhost` which points to `127.0.0.1`.

## Set up
The project runs a configurable list of `Kafka` servers and the ELK stack locally at a staging directory specified in `config.yml`. It takes `json` formatted log files and put the entries into a `kafka` topic as specified in `sources` in `config.yaml`. All input files have to be stored in `files` directory.

## Run
To create/restart the servers:
```
ansible-playbook playbook.yaml
```
It would take a long time for `docker` to build the images the first time around depending on the internet speed. It might appear to be stuck at `run compose` but really it is just downloading all the components. Be patient and let the build finish.

To access `kafka`, you will need a `zookeeper` connection string. `zookeeper` runs at `"{% for kafka_id in kafka_list %}0.0.0.0:{{ zookeeper_client_port_start + kafka_id }}{% if not loop.last %},{% endif %}{% endfor %}"`. `zookeeper` connection string by default is `127.0.0.1:2181,127.0.0.1:2182,127.0.0.1:2183`.

To access `kibana`, point your browser to `127.0.0.1:{{ kibana_port }}` which by defaul is `127.0.0.1:5601`.

To manage the `elasticsearch` instance, point your browser to `127.0.0.1:{{ elasticsearch_port }}/_plugin/kopf/` which by defaul is `127.0.0.1:9200/_plugin/kopf/`.

To stop and remove the servers:
```
ansible-playbook playbook_stop.yaml
```

Because some of the files are generated using another role(`elasticsearch`), use `sudo` for removing those files. 
To remove the generated directory(**irreversible**):
```
sudo ansible-playbook playbook_clean.yaml
```
