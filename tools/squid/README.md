# Squid  <!-- omit in toc -->

- [Description](#description)
- [Installation](#installation)
- [Enable Unix service](#enable-unix-service)
- [Default configuration](#default-configuration)

## Description

Squid is a proxy server

## Installation

```sh
sudo yum update
sudo yum install squid
```

## Enable Unix service

```sh
sudo service squid start
sudo service squid status -l
sudo service squid restart
```

## Default configuration

- **Configuration file** : /etc/squid/squid.conf
- **Log file** : /var/log/squid/
- **Default Port** : 3128
