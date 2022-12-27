# Windows Subsystem for Linux 2 (WSL 2) <!-- omit in toc -->

- [](#)
  - [Requirement](#requirement)
  - [Installation](#installation)
  - [Add new Box](#add-new-box)

## Installation

 [Microsoft process](https://docs.microsoft.com/fr-fr/windows/wsl/install-win10)


## WSL commande

```dos

# List distro and version
ws -l -v

# Set defaut version of WSL to 2
wsl --set-default-version 2

# Start distro (The current pat is mount un the distro)
cd d:\workspace\distro
wsl -d Ubuntu-20.04
```

## Configuration Reseau

```bash
# If the commande below failed
ping google.com

# Configure the network with
sudo bash -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'
```

## Install Ansible

```bash

# Install
sudo apt-get update
sudo apt install software-properties-common
sudo apt install ansible

# Test install 
ansible localhost -m ping
```