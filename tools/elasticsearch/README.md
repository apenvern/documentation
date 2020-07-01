# Tool Box Elasticsearch <!-- omit in toc -->

- [Procédure d'installation](#procédure-dinstallation)
- [Administration](#administration)
- [Installation d'un plugin](#installation-dun-plugin)

## Procédure d'installation

**Mode opératoire d'installation :**

```sh
# Télécharger la dernière version
wget https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.0.0/elasticsearch-2.0.0.tar.gz
mkdir -p /appli/tools
cd /appli/tools
# On extrait que le necessaire
tar xvzf /tmp/elasticsearch-2.0.0.tar.gz
ln -s elasticsearch-2.0.0/ elasticsearch
```

## Administration

**Démarrer ES :**

```sh
# Positionner la Jdk (si besoin)
export JAVA_HOME=/appli/tools/jdk/

cd /appli/tools/elasticsearch
bin/elasticsearch -d
```

En cas de problème pour accéder à Elasticsearch depuis une machine distante. Il faut éditer le fichier de configuration et ajouter la propriété network.host avec la valeur *_non_loopback_*. [Pour plus d'informations](https://www.elastic.co/blog/elasticsearch-unplugged)

**Arrêter ES :**

```sh
# TODO trouver un moyen plus "propre"
ps auxwww | grep elasticsearch
kill -9
```

## Installation d'un plugin

```sh
# Positionner la Jdk (si besoin)
export JAVA_HOME=/appli/tools/jdk/

cd /appli/tools/elasticsearch
/appli/xxxxx/retro_doc/tools/elasticsearch/bin/plugin install lmenezes/elasticsearch-kopf/v2.0.0

# TODO vérifier la conf
# En cas de proxy :
/appli/xxxx/retro_doc/tools/elasticsearch/bin/plugin install lmenezes/elasticsearch-kopf/v2.0.0 -Dhttp.proxyHost=hostname -Dhttp.proxyPort=8080
```