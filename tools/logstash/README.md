# Tool Box Logtash

<!-- MarkdownTOC depth=3 -->

- [Procédure d'installation](#procédure-dinstallation)
- [Script de démarrage](#script-de-démarrage)
- [Installation d'un plugin](#installation-dun-plugin)
    - [Procédure sous Windows](#procédure-sous-windows)
    - [Procédure sous Unix](#procédure-sous-unix)
- [Création d'un plugin logstash](#création-dun-plugin-logstash)
    - [Init du plugin](#init-du-plugin)
    - [Compilation d'un plugin logstash](#compilation-dun-plugin-logstash)
    - [Installation du plugin depuis le repo local](#installation-du-plugin-depuis-le-repo-local)

<!-- /MarkdownTOC -->

## Procédure d'installation

**Mode opératoire d'installation :**

```sh
# Télécharger la dernière version
wget https://download.elastic.co/logstash/logstash/logstash-2.0.0.tar.gz
mkdir -p /appli/tools
cd /appli/tools
tar xvzf /tmp/logstash-2.0.0.tar.gz
ln -s logstash-2.0.0/ logstash
```

## Script de démarrage

```bash
#!/bin/bash
########################################################################################
# 
# Description : Script de demarrage de logstash
# Usage : ./start-logstash.sh 
#
########################################################################################
#
# Revision 1.0 2015/10/13 apenvern : Init du script
#
########################################################################################

export JAVA_HOME=/appli/tools/jdk/
/appli/tools/logstash/bin/logstash agent -f /appli/conf/logstash/logstash.conf
```

## Installation d'un plugin

### Procédure sous Windows 

Positionner les variables d'environnement (facultatif)
```bat
rem La Jdk 
SET JAVA_HOME=D:\outils\Java\jdk1.8.0_25
SET PATH=%JAVA_HOME%\bin;%PATH%

rem Jruby permet notamment de forcer la version embarquer dans Logstash. Override celle qui est dans le path.
SET JRUBY_HOME=D:\outils\logstash\logstash-2.0.0\vendor\jruby
SET PATH=%JRUBY_HOME%\bin;%PATH%

rem La configuration Proxy
SET http_proxy=http://login:pwd@host:port
SET https_proxy=http://login:pwd@host:port
```

La commande d'installation d'un plugin :
```bat
D:\outils\logstash\logstash-2.0.0\bin\plugin.bat install logstash-filter-translate
```

### Procédure sous Unix

Positionner les variables d'environnement (facultatif)
```sh
# La Jdk 
export JAVA_HOME=/appli/tools/jdk/

# La configuration Proxy
export http_proxy=http://login:pwd@host:port
export https_proxy=http://login:pwd@host:port
```

La commande d'installation d'un plugin :
```sh
/appli/tools/logstash/bin/plugin install logstash-filter-translate
```

## Création d'un plugin logstash

### Init du plugin

TODO il manque la partie init. 

- Clone du repo github de ES pour la création de plugin 
- Renommage des fichiers

### Compilation d'un plugin logstash

Positionner la version Jruby dans le path. On force la version inclue dans les binaires de Logstash. 
```bat
SET JRUBY_HOME=D:\outils\logstash\logstash-2.0.0\vendor\jruby
SET PATH=%JRUBY_HOME%\bin;%PATH%
```

Configurer le proxy si besoin 
```bat
set http_proxy=http://login:pwd@host:port
set https_proxy=http://login:pwd@host:port
```

Installer le Bundler gem si besoin 
```bat
> gem install bundle
io/console not supported; tty will not be manipulated
Fetching: bundler-1.10.6.gem (100%)
Successfully installed bundler-1.10.6
Fetching: bundle-0.0.1.gem (100%)
Successfully installed bundle-0.0.1
2 gems installed
```

Installer les dépendances 
```bat
rem Descendre dans le répertoire de votre nouveau plugin
cd D:/workspace/perso/logstash-plugin/logstash-filter-sequence

> bundle install
io/console not supported; tty will not be manipulated
Fetching gem metadata from https://rubygems.org/..........
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Resolving dependencies.....
Installing rake 10.4.2
Installing cabin 0.7.2
....
Installing logstash-devutils 0.0.18
Using logstash-filter-sequence 2.0.3 from source at .
Using bundler 1.10.6
Bundle complete! 2 Gemfile dependencies, 34 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

Lancer les tests 
```bat
> bundle exec rspec
io/console not supported; tty will not be manipulated
io/console not supported; tty will not be manipulated
No examples found.

Finished in 0.003 seconds (files took 0.221 seconds to load)
0 examples, 0 failures
```

### Installation du plugin depuis le repo local

Trouver ou est installe votre plugin dans le repo 
```bat
> bundle show logstash-filter-sequence
io/console not supported; tty will not be manipulated
D:/workspace/perso/logstash-plugin/logstash-filter-sequence
```

Configurer le Gemfile de logstash pour y ajouter la ligne ci-dessous
```ruby
gem "logstash-filter-sequence", :path => "D:/workspace/perso/logstash-plugin/logstash-filter-sequence"
```

Installer le plugin
```bat
> D:\outils\logstash\logstash-2.0.0\bin\plugin.bat install --no-verify
io/console not supported; tty will not be manipulated
Installing...
Installation successful
```

#### Contribuer le plugin sur RubyGems

Créer un compte sur [rubygems](https://rubygems.org/) et récupérer la clé de l'api.

```sh
curl -u username:password https://rubygems.org/api/v1/api_key.yaml > ~/.gem/credentials
chmod 0600 ~/.gem/credentials
```

Créer un fichier à la racine du projet avec le contenu ci-dessous :
```ruby
require "logstash/devutils/rake"
```

Lancer les commandes ci-dessous :
```bat
> bundle install
> bundle exec rake vendor
> bundle exec rspec
> bundle exec rake publish_gem
```
