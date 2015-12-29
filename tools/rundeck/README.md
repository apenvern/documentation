
# Tool Box Rundeck

<!-- MarkdownTOC depth=3 -->

- [Script de démarrage de Rundeck](#script-de-démarrage-de-rundeck)
- [Configuration de Rundeck](#configuration-de-rundeck)
    - [Ajouter un node](#ajouter-un-node)

<!-- /MarkdownTOC -->

## Script de démarrage de Rundeck

```bash
#!/bin/bash
########################################################################################
# 
# Description : Script de demarrage de rundeck
# Usage : ./start-rundeck.sh 
#
########################################################################################
#
# Revision 1.0 2015/10/13 apenvern : Init du script
#
########################################################################################

export JVM_OPTIONS="-XX:MaxPermSize=256m -Xmx1024m -Xms256m -server"
export JAVA_HOME=../jdk/
$JAVA_HOME/bin/java $JVM_OPTIONS -jar rundeck-launcher-2.6.0.jar
```

## Configuration de Rundeck

### Ajouter un node 

Pour ajouter un node il faut modifier le fichier **rundeck_home/project_name/etc/resources.xml**

```xml
    <node name="Machine1 - Rec - 1" type="Node" 
        description="Noeud 1 de la machine1 en recette."
        hostname="machine1.domaine" 
        username="username" 
        osFamily="Linux" 
        osVersion="2.6.32" 
        osArch="64"
        tags="recette,redhat" 
        file-copy-destination-dir="/tmp/"
    />
```
