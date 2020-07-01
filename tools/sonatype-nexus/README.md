
# Tool Box Sonatype Nexus <!-- omit in toc -->

<!-- MarkdownTOC depth=3 -->

- [Procédure d'installation](#procédure-dinstallation)
- [Administration](#administration)
- [Configuration de Maven](#configuration-de-maven)
- [Nexus Core API REST API](#nexus-core-api-rest-api)

<!-- /MarkdownTOC -->

## Procédure d'installation

**Mode opératoire d'installation :**

```sh
# Télécharger la dernière version
wget https://sonatype-download.global.ssl.fastly.net/nexus/oss/nexus-2.11.4-01-bundle.tar.gz
mkdir -p /appli/pic/tools
cd /appli/pic/tools
# On extrait que le nécessaire
tar xvzf /tmp/nexus-2.11.4-01-bundle.tar.gz nexus-2.11.4-01
ln -s nexus-2.11.4-01/ nexus
```

**Configuration de Nexus :**

```sh
# Création du repo Nexus (répertoire ou seront stockées toutes les librairies)
 mkdir -p /appli/pic/workspaces/nexus-repo
# Ouvrir le fichier /appli/pic/tools/nexus/conf/nexus.properties et mettre à jour la clé nexus-work
nexus-work=/appli/pic/workspaces/nexus-repo
```

## Administration

**Démarrer le Nexus :**

```sh
cd /appli/pic/tools/nexus/bin
./nexus start
```

**Arrêter le Nexus :**

```sh
cd /appli/pic/tools/nexus/bin
./nexus stop
```

## Configuration de Maven

```xml
<!-- Configuration setting.xml -->
<servers>
<server>
   <id>nexus.releases</id>
   <username>username</username>
   <password>password</password>
</server>
<server>
   <id>nexus.snapshots</id>
   <username>username</username>
   <password>password</password>
</server>
</servers>

<mirrors>
<mirror>
   <id>mirror.release</id>
   <url>http://host:port/content/groups/public/</url>
   <mirrorOf>external:*</mirrorOf>
</mirror>
<mirror>
   <id>mirror.snapshots</id>
   <url>http://host:port/content/repositories/snapshots/</url>
   <mirrorOf>snapshots</mirrorOf>
</mirror>
</mirrors>

<!-- Configuration Pom -->
<distributionManagement>
   <snapshotRepository>
      <id>nexus.snapshots</id>
      <name>Nexus snapshots repository</name>
      <url>http://host:port/content/repositories/snapshots/</url>
   </snapshotRepository>
   <repository>
      <id>nexus.releases</id>
      <name>Nexus relases repository</name>
      <url>http://host:port/content/repositories/releases/</url>
   </repository>
</distributionManagement>
```

## Nexus Core API REST API

Exemple d'appel en sh à l'API du Nexus :

```sh

# Lister toutes les lib avec le groupip "fr.test"
curl -s -X GET -u admin:admin123 "http://localhost:8081/nexus/service/local/lucene/search?g=fr.test"

# Télécharger depuis le Nexus une lib
wget -q --user=admin --password=admin123 "http://localhost:8081/nexus/service/local/artifact/maven/content?g=fr.test&a=test-api&v=LATEST&r=releases&e=jar" -O /tmp/test-api-LASTEST.jar

# Upload sur le nexus une librairie
curl -v -F r=releases -F hasPom=false -F e=jai -F g=fr.test -F a=test-api -F v=1.0 -F p=zip -F file=@test-api-1.0.zip -u admin:admin123 http://localhost:8081/nexus/service/local/artifact/maven/content
```
