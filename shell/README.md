
# Tool Box Unix

<!-- MarkdownTOC depth=3 -->

- [Informations système](#informations-système)
    - [OS](#os)
    - [CPU](#cpu)
    - [Swap](#swap)
    - [File System](#file-system)
    - [User](#user)
    - [Port](#port)
- [Les commandes unix](#les-commandes-unix)
    - [find](#find)
    - [tar](#tar)
    - [sed](#sed)
    - [nohup](#nohup)
    - [scp | lftp](#scp-|-lftp)
    - [md5 | checksum | digest](#md5-|-checksum-|-digest)
    - [ssh](#ssh)
- [Tips](#tips)

<!-- /MarkdownTOC -->

## Informations système

### OS

```ksh 

#!/bin/ksh
# ndd (Solaris)
for var in $(/usr/sbin/ndd /dev/tcp \? | awk '{print $1}'| grep -v obsolete );do
        echo "--------------------"
        echo "------"$var
        /usr/sbin/ndd /dev/tcp $var
done

# prtdiag (Solaris)
/usr/platform/sun4u/sbin/prtdiag -v 

# /etc/release (Solaris)
more /etc/release
                       Solaris 8 2/04 s28s_hw4wos_05a SPARC
           Copyright 2004 Sun Microsystems, Inc.  All Rights Reserved.
                            Assembled 08 January 2004

# isainfo (Solaris)
/usr/bin/isainfo -kv
64-bit sparcv9 kernel modules
```

### CPU 

> Sous Solaris la conso ne dépasse pas les 100% c.a.d nb cpu * consommation max = 100%
> 
> Sous Unix la conso max est de 100% par CPU. Ce qui fait que la conso d'un process peux être à 200%
 
#### Info sur le nombre de cpu logique (solaris)
```bash
kstat cpu_info
```

### Swap
```bash
# La commande ps 
swap -s
total: 3213920k bytes allocated + 6107288k reserved = 9321208k used, 1459312k available

# La commande ps 
# rss > ram réel
# vsz > ram adressable
ps -aefo pid,rss,vsz,args

14470 590864 781800 /soft/IBM/WebSphere/AppServer/java/bin/java -XX:MaxPermSize=256m -Dwas.s
15389 539648 742584 /soft/IBM/WebSphere/AppServer/java/bin/java -XX:MaxPermSize=256m -Dwas.s
24722 214848 338776 /usr/java/bin/java -XX:+UseParallelGC -Xms64m -server -XX:ParallelGCThreads=4 -
13747 502912 698184 /soft/IBM/WebSphere/AppServer/java/bin/java -XX:MaxPermSize=256m -Dwas.s
12377 501552 720472 /soft/IBM/WebSphere/AppServer/java/bin/java -XX:MaxPermSize=256m -Dwas.s
```

### File System

```bash 

# Taille des FS
df -kh

# Taille des répertoires
du -s *
du -sh * 

# Lister les fichiers lockés par un process
# lsof
lsof -p 29962
# ls
ls -l /proc/29962/fd

```

### User

```
# Récupérer les info du user
id -a syscft
uid=311(syscft) gid=311(cft) groups=6645(testgrp),583(testgrp1)

# Création group
/usr/sbin/groupadd -g 311 cft

# Création utilisateurs :
useradd -c "Compte Cft" -d /users/syscft -g cft -G testgrp -m -u 311 -s /usr/bin/ksh syscft

# On définit un password
passwd syscft (-> test 2 fois) :
New Password:
Re-enter new Password:
passwd: password successfully changed for syscft 
```


### Port

```bash

# Afficher la plage des ports dynamiques
/usr/sbin/ndd /dev/tcp tcp_smallest_anon_port tcp_largest_anon_port
9000
65535

# Trouver le process qui ecoute sur un port
for i in `ps -eaf | grep java | grep adm | cut -d ' ' -f4`; do pfiles $i | grep 60016 && echo $i; done;
```

## Les commandes unix

### find

```bash

# find exec
# Recherche tous les fichiers/dossiers CVS et les supprime
find . -name CVS -exec rm -r {} \;

# find xargs
# Recherche tous les fichiers qui contiennent Metro dans leur nom et lance un grep dessus
find . -name '*Metro*' -type f | xargs grep -v OK > Metro.log

#Trouver tous les fichiers modifié avant aujourdh'ui 
find ./ -mtime +0 -name "*.log" 

```


### tar

```bash

# Extraction de plusieurs archives
for i in `find . -name \*.tar.gz`; do  rep_name=`echo $i | cut -d"." -f1,2`; mkdir $rep_name; tar xvzf $i -C $rep_name; done

```

### sed

```bash

# Remplace tous les espaces
sed -e 's/  */ /g' 

```

### nohup

```bash

# Lance un script en tache de fond
nohup ./mon_script.sh &

```

### scp | lftp

```bash

# Copie de fichier d'une machine vers une autre 
scp mon_fichier.tar.gz root@machine2:/tmp/rep/

```

### md5 | checksum | digest

```
# md5
{ oracle } HEAD » md5sum tool-box.md
ed3a50830e10066681aedf127f57516d *tool-box.md

# cksum
{ oracle } HEAD » cksum tool-box.md
2047550445 2101 tool-box.md

# Digest (Solaris)
digest -a md5 -v /path/to/file
```

### ssh

```bash
# Tunnel depuis la machine1 avec le compte root sur le port 80 vers la machine2 sur le port 7060
ssh -g -L 80:machine2:7060 root@machine2
# Tester la connection dans un navigateur (vérifier le proxy) http://machine1:80/endpoint/
```

## Tips

**Récupérer les sorties standarts de plusieurs commandes**

```bash
$ false | true
$ echo "${PIPESTATUS[0]} ${PIPESTATUS[1]}"
1 0
```

**Trouver une classe dans une liste de jar**

```bash
for i in `find . -name \*.jar`; do jar tvf $i|grep -qi <MA_CLASSE> && echo $i ; done
```

**Lister les serialUID des jar**

```bash
for i in `find . -name \*.jar`; do echo "Jar : $i"; for j in `jar tf $i | grep class | cut -d"." -f1 | sed -e 's/\//./g'` ; do serialver -classpath $i $j| grep fr ; done; done
```

