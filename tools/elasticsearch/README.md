# Tool Box Elasticsearch

<!-- MarkdownTOC depth=3 -->

- [Procédure d'installation](#procédure-dinstallation)
- [Administration](#administration)
- [Installation d'un plugin](#installation-dun-plugin)

<!-- /MarkdownTOC -->

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
/appli/gessica/retro_doc/tools/elasticsearch/bin/plugin install lmenezes/elasticsearch-kopf/v2.0.0

# TODO vérifier la conf 
# En cas de proxy : 
/appli/gessica/retro_doc/tools/elasticsearch/bin/plugin install lmenezes/elasticsearch-kopf/v2.0.0 -Dhttp.proxyHost=hostname -Dhttp.proxyPort=8080
```

