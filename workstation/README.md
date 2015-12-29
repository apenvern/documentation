# Workstation

Commande d'installation d'un poste de dev

## Unix

```sh
# Mise à jour des packets
sudo apt-get update

# Les packages de base pour le dev
sudo apt-get install -y build-essential
```

## Git

```sh
# Installation de Git
sudo apt-get install git
# Configuration de Git
git config --global user.name "apenvern"
git config --global user.email "arnaud.pe.it@gmail.com"
```

## Java

```sh
# Installation de la Jdk

sudo apt-get install openjdk-8-jdk

# Activer  une version de Java
# update-java-alternatives -l
# java-1.7.0-openjdk-amd64 1071 /usr/lib/jvm/java-1.7.0-openjdk-amd64
# java-1.8.0-openjdk-amd64 1069 /usr/lib/jvm/java-1.8.0-openjdk-amd64

sudo update-java-alternatives -s /usr/lib/jvm/java-1.8.0-openjdk-amd64
```

## Eclipse

```sh
# Installation eclipse 
wget -N http://eclipse.ialto.com/technology/epp/downloads/release/luna/SR2/eclipse-jee-luna-SR2-linux-gtk-x86_64.tar.gz
sudo mkdir /opt/eclipse
gunzip eclipse-jee-luna-SR2-linux-gtk-x86_64.tar.gz
sudo tar xvf eclipse-jee-luna-SR2-linux-gtk-x86_64.tar -C /opt/eclipse
sudo mv /opt/eclipse/eclipse /opt/eclipse/eclipse-luna
sudo ln -sfn eclipse-luna /opt/eclipse/latest
printf "export ECLIPSE_HOME=/opt/eclipse/latest\nexport PATH=\$PATH:\$ECLIPSE_HOME" | sudo tee /etc/profile.d/eclipse.sh
. /etc/profile.d/eclipse.sh
```

## Maven 

```sh
#Installation Maven 
wget -N http://apache.crihan.fr/dist/maven/maven-3/3.3.3/binaries/apache-maven-3.3.3-bin.tar.gz
sudo mkdir /opt/maven
sudo tar xvzf apache-maven-3.3.3-bin.tar.gz -C /opt/maven
sudo ln -sfn apache-maven-3.3.3 /opt/maven/latest
printf "export MAVEN_HOME=/opt/maven/latest\nexport PATH=\$PATH:\$MAVEN_HOME/bin" | sudo tee /etc/profile.d/maven.sh
. /etc/profile.d/maven.sh
```

## Gradle

```sh
# Installation Gradle 
# installs to /opt/gradle
# existing versions are not overwritten/deleted
# seamless upgrades/downgrades
# $GRADLE_HOME points to latest *installed* (not released)
gradle_version=2.4
wget -N http://services.gradle.org/distributions/gradle-${gradle_version}-all.zip
sudo unzip gradle-${gradle_version}-all.zip -d /opt/gradle
sudo ln -sfn gradle-${gradle_version} /opt/gradle/latest
printf "export GRADLE_HOME=/opt/gradle/latest\nexport PATH=\$PATH:\$GRADLE_HOME/bin" | sudo tee /etc/profile.d/gradle.sh
. /etc/profile.d/gradle.sh
# check installation
gradle -v
```

## Docker

```sh
# Installation docker
wget -qO- https://get.docker.com/ | sh
```

## Node JS

```sh
# Installation de NodeJS
# Si besoin installer curl 
sudo apt-get install curl
curl -sL https://deb.nodesource.com/setup | sudo bash -
sudo apt-get install -y nodejs

# Update Nodejs 
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```

## NPM

```sh
# Problem d'acces sur le repertoire npm pour plus de détails cf : https://docs.npmjs.com/getting-started/fixing-npm-permissions
mkdir ~/npm-global
npm config set prefix '~/npm-global'
printf "export NPM_HOME=~/npm-global\nexport PATH=\$NPM_HOME/bin:\$PATH\nexport NODE_PATH=$NODE_PATH:/\$NPM_HOME/lib/node_modules" | sudo tee /etc/profile.d/npm.sh
. /etc/profile.d/npm.sh

# Mise à jour de tous les packages
npm install -g
```

## Yeoman

```sh
# Installation de Yeoman
npm install -g yo
# En cas d'erreur : 
npm install -g yo --unsafe-perm
```

## Bower

```sh
# Installation de Bower
npm install -g bower
```

## Grunt

```sh
# Installation de Grunt
npm install -g grunt-cli
```

## JHipster
```sh
# Installation de JHipster
npm install -g generator-jhipster

# JHipster avec Docker
sudo docker pull jdubois/jhipster-docker
mkdir ~/jhipster
sudo docker run -v ~/jhipster:/jhipster -p 8080:8080 -p 3000:3000 -p 3001:3001 -p 4022:22 -t jdubois/jhipster-docker
```

## Sublime Text 3

[rubygems](https://rubygems.org/)

Liste des plugins : 

- [TypeScript](https://packagecontrol.io/packages/TypeScript) : Plugin pour développer en TypeScript
- [DocBlockr](https://packagecontrol.io/packages/DocBlockr) : Plugin pour la doc (ie JavaDoc)
- [Emmet](https://packagecontrol.io/packages/Emmet) : Plugin pour Html et Css
    En cas de problème lors de l'installation sur PyV8 il faut installer les binaires manuellement : [binaires](https://github.com/emmetio/pyv8-binaries). A noter que sur SublimeText 3 il faut coller les binaires dans D:\outils\Sublime Text\Data\\**Installed Packages**\PyV8\pyv8-win64-p3
- [SideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements) : Ajout de nombreux d'élément au menu de la SideBar
- [Tag](https://github.com/titoBouzout/Tag) : Outil pour du Html

Lien utils : 

- Top 10 des plugin : http://www.smartrock.fr/blog/10-plugins-sublime-text-indispensables/

