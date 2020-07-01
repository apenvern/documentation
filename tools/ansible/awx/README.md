# Tool Box AWX <!-- omit in toc -->

- [Install AWX](#install-awx)
  - [Requirement](#requirement)
  - [Installation](#installation)
  - [Test connection](#test-connection)
- [Reference](#reference)

## Install AWX

### Requirement

#### Ansible version 2.4+

```sh
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

#### Other requirements

- docker

### Installation

```sh
# Cloner the AWX repository
git clone https://github.com/ansible/awx.git

# Launch installation via Ansible
cd awx/installer/
ansible-playbook -i inventory install.yml

# Check docker container logs
sudo docker logs -f awx_task
```

### Test connection

- **url** : <http://Adresse_IP_de_votre_serveur/>
- **login** : admin
- **password** : password

## Reference

- <https://www.howtoforge.com/tutorial/how-to-install-ansible-awx-with-docker-on-centos/>
