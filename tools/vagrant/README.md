# Vagrant <!-- omit in toc -->

- [Vagrant Private Repository](#vagrant-private-repository)
  - [Requirement](#requirement)
  - [Installation](#installation)
  - [Add new Box](#add-new-box)

## Vagrant Private Repository

### Requirement

- Apache Httpd
- Python & pip
- vagrant-metadata

### Installation

```sh
# Install Httpd
yum -y install httpd
systemctl start httpd.service
systemctl enable httpd.service

# Configure Httpd !! Warning Configuration not secured Warning !!
vi /etc/httpd/conf.d/00-ftp-hostname.lan.conf

<VirtualHost *:80>
  ServerAdmin email
  DocumentRoot "/var/www/ftp"
  ServerName hostname.lan
  ServerAlias hostname
  ErrorLog logs/ftp-hostname.lan-error_log
  CustomLog logs/ftp-hostname.lan-access_log common
  <Directory />
    Options Indexes
    Require all granted
  </Directory>
</VirtualHost>

# Disable the default conf by comment the following file
vi /etc/httpd/conf/httpd.conf
DocumentRoot "/var/www/html"
...
<Directory "/var/www/html">
  Options Indexes FollowSymLinks
  AllowOverride None
  Require all granted
</Directory>

#Install PIP
yum -y install python-pip
pip install --upgrade pip

#Install vagrant-metadata
pip install vagrant-metadata

# Create Vagrant directory for serve Boxes
mkdir -p /var/www/ftp/vagrant-boxes/xxxxx/
```

### Add new Box

```sh
# Create directory for the new Boxes
mkdir -p /var/www/ftp/vagrant-boxes/xxxxx/vagrant-java-angulars-dev-box

# Create directory for the version and the provider
mkdir -p /var/www/ftp/vagrant-boxes/xxxxx/vagrant-java-angulars-dev-box/0.0.1/virtualbox

# Copy Box
scp ./vagrant-java-angulars-dev-box-0.0.2.box login@hostname:/var/www/ftp/vagrant-boxes/xxxxx/vagrant-java-angulars-dev-box/0.0.1/virtualbox/

# Update the vagrant Metadata
vagrant-metadata --name="xxxxx/vagrant-java-angulars-dev-box" --description="Box Vagrant contenant les outils nécessaires aux développements pour des projets ayant comme socle technique Java et Angular" --baseurl="http://hostname.lan/vagrant-boxes/xxxxx/vagrant-java-angulars-dev-box/"
```
