
# Tool Box Unix <!-- omit in toc -->

- [Informations système](#informations-système)
  - [OS](#os)
  - [CPU](#cpu)
  - [Swap](#swap)
  - [File System](#file-system)
  - [User](#user)
  - [Port](#port)
  - [Réseau](#réseau)
  - [Cle ssh](#cle-ssh)
- [Les commandes unix](#les-commandes-unix)
  - [find](#find)
  - [tar](#tar)
  - [sed](#sed)
  - [nohup](#nohup)
  - [scp | lftp](#scp--lftp)
  - [md5 | checksum | digest](#md5--checksum--digest)
  - [ssh](#ssh)
  - [Openssl - création de certificats](#openssl---création-de-certificats)
- [Tips](#tips)
  - [Récupérer les sorties standarts de plusieurs commandes](#récupérer-les-sorties-standarts-de-plusieurs-commandes)
  - [Définir une valeur par defaut pour une variable](#définir-une-valeur-par-defaut-pour-une-variable)
  - [Vérifier si une variable est définie](#vérifier-si-une-variable-est-définie)
  - [Trouver une classe dans une liste de jar](#trouver-une-classe-dans-une-liste-de-jar)
  - [Lister les serialUID des jar](#lister-les-serialuid-des-jar)
  - [Liens utiles](#liens-utiles)

## Informations système

### OS

#### Connaitre la distribution Linux et sa version

```bash
[root@machine-01 ~]# cat /etc/redhat-release
Red Hat Enterprise Linux Server release 5.11 (Tikanga)

[root@machine-01 ~]# hostnamectl
   Static hostname: localhost.localdomain
Transient hostname: machine-01
         Icon name: computer-vm
           Chassis: vm
        Machine ID: xxxxx
           Boot ID: xxxx
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-229.el7.x86_64
      Architecture: x86_64
```

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

- Sous Solaris la conso ne dépasse pas les 100% c.a.d nb cpu * consommation max = 100%
- Sous Unix la conso max est de 100% par CPU. Ce qui fait que la conso d'un process peux être à 200%

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

#### Gestion de la taille disque sur une VM

**Rappels :**

- **Volume physique (PV)** : Un volume physique ou « PV » pour « physical volume » est un disque ou une partition. C'est un espace de stockage bien réel (autrement dit un périphérique de la forme /dev/sda2 par exemple), dont on va confier la gestion à LVM.
- **Groupe de volumes (VG)** : Un groupe de volumes ou « VG » pour « volume group » est, comme son nom l'indique, un ensemble de PV. On a donc un ou plusieurs PV dans un groupe de volumes. Pour utiliser LVM, il faut obligatoirement au moins un groupe de volumes.
- **Volume Logique (LV)** : Un volume logique ou « LV » pour « logical volume » est ce que nous allons utiliser au final. Un volume logique est un espace « quelque part dans un VG » où l'on peut mettre un système de fichiers. C'est ce qui remplace les partitions. On peut donc utiliser un volume logique pour /home, un autre pour la racine, un autre pour mettre la mémoire virtuelle, etc…

```bash

# Liste les disque disponible
[root@machine01 ~]# ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb  /dev/sdc

# Liste les disques physiques installés
# A noter la différence entre le ls /dev/ds* et le pvs permet de connaitre le nom du disque à ajouter
[root@machine01 ~]# pvs
  PV         VG      Fmt  Attr PSize   PFree
  /dev/sda2  VGRoot  lvm2 a--   59,88G 7,19G
  /dev/sdb   vg_gael lvm2 a--  170,00G 4,00G

# Creation du disque physique
[root@machine01 ~]# pvcreate /dev/sdc
  Writing physical volume data to disk "/dev/sdc"
  Physical volume "/dev/sdc" successfully created

# Lister les disques physiques
[root@machine01 ~]# pvdisplay
  "/dev/sdc" is a new physical volume of "100,00 GB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc
  VG Name
  PV Size               100,00 GB
  Allocatable           NO
  PE Size (KByte)       0
  Total PE              0
  Free PE               0
  Allocated PE          0

# Creer un groupe virtuel
[root@machine01 ~]# vgcreate lvm /dev/sdc

# Creer un volume logique
# Cette commande va créer le LV test d’une taille de 10Go sur le VG nommé lvm.
[root@machine01 ~]# lvcreate -L 10G -n test lvm

# Lister les groupes virtuel
[root@machine01 ~]# vgs
  VG      #PV #LV #SN Attr   VSize   VFree
  lvm    2   7   0 wz--n- 159,84G 107,16G
  vg_gael   1  11   0 wz--n- 170,00G   4,00G

# Ajout d'un disque physique à un groupe virtuel.
[root@machine01 ~]# vgextend lvm /dev/sdc
  Volume group "lvm" successfully extended

# Agrandir un volume logique
[root@machine01 ~]# lvresize -L+100g -n /dev/mapper/lvm-appli
  Extending logical volume appli to 109,75 GB
  Logical volume appli successfully resized

# Agrandir un volume logique
[root@machine01 ~]# resize2fs /dev/mapper/lvm-appli
resize2fs 1.39 (29-May-2006)
Filesystem at /dev/mapper/lvm-appli is mounted on /appli; on-line resizing required
Performing an on-line resize of /dev/mapper/lvm-appli to 28770304 (4k) blocks.
Le système de fichiers /dev/mapper/lvm-appli a maintenant une taille de 28770304 blocs.

```

### User

```sh
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

### Réseau

```sh
# Modifier le fichier /etc/hosts
[root@machine01 ~]$ vi /etc/hosts
# Do not remove the following line, or various programs
# that require network functionality will fail.
127.0.0.1       localhost.localdomain   localhost
::1     localhost.localdomain   localhost6      localhost
127.0.0.1       machine01
```

### Cle ssh

```sh
# Generer un cle ssh
[root@machine01 ~]$ ssh-keygen -t rsa -C "your_email@youremail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/test/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/test/.ssh/id_rsa.
Your public key has been saved in /home/test/.ssh/id_rsa.pub.
The key fingerprint is:
ac:e4:e4:e4:e4:e4:e4:e4:c6:45:14:c9:8d:7a:a1:09 your_email@youremail.com

# Propager la clee public ssh.

# Copier la cle public sur le serveur remote
[root@machine01 ~]$ scp .ssh/id_rsa.pub login@hostname:.ssh/id_rsa.pub
The authenticity of host 'hostname' can't be established.
RSA key fingerprint is ac:e4:e4:e4:e4:e4:e4:e4:c6:45:14:c9:8d:7a:a1:09.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'hostname' (RSA) to the list of known hosts.
login@hostname's password:
id_rsa.pub                                                                                                              100%  407     0.4KB/s   00:00

# Se connecter sur le serveur remote
root@machine02 $ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
root@machine02 $ rm ~/.ssh/id_rsa.pub
root@machine02 $ chmod 660 ~/.ssh/authorized_keys
root@machine02 $ chmod 700 ~/.ssh

# Ajouter la passphrase à l'agent sur le serveur principale
# La commande met sur la sortie standard des variables environnement à déclarer et à exporter.
[root@machine01 ~]$ ssh-agent
SSH_AUTH_SOCK=/tmp/ssh-cpaWt12729/agent.12729; export SSH_AUTH_SOCK;
SSH_AGENT_PID=12730; export SSH_AGENT_PID;
echo Agent pid 12730;

[root@machine01 ~]$ ssh-add
Enter passphrase for /home/root/.ssh/id_rsa:
Identity added: /home/root/.ssh/id_rsa (/home/root/.ssh/id_rsa)

# Propager la clee privee ssh (Attention a ne pas copier la clee privee n'importe ou).
# copîer la clee priver dans le répertoire <home>/.ssh/
cp /tmp/ma_clee_privee /home/mon_user/.ssh/id_rsa
# Mettre les droits en lecture seul
chmod 400 /home/mon_user/.ssh/id_rsa
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

```sh
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

### Openssl - création de certificats

```bash
alias@host /d/ssh
$ openssl genrsa -des3 -passout pass:x -out server.pass.key 2048
Generating RSA private key, 2048 bit long modulus
.................................................................+++
..................+++
e is 65537 (0x10001)

alias@host /d/ssh
$ openssl rsa -passin pass:x -in server.pass.key -out server.key
writing RSA key

alias@host /d/ssh
$ rm server.pass.key

# Création du certificat avec un mdp vide
alias@host /d/ssh
$ openssl req -new -key server.key -out server.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:Country
State or Province Name (full name) [Some-State]:State
Locality Name (eg, city) []:Locality
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Organization Name
Organizational Unit Name (eg, section) []:Organizational
Common Name (e.g. server FQDN or YOUR name) []:Name
Email Address []:Email

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

alias@host /d/ssh
$ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
Signature ok
subject=/C=FR/ST=Xxxxx/L=Yyyyyy/O=132 - Tertiaire/OU=Zzzzz/CN=Wwwww/emailAddress=xxxx.xxxx@xxxxxx.com
Getting Private key

alias@host /d/ssh
$ rm server.csr

# Télécharger un certificat depuis un serveur web
[alias@host ~]$ echo -n | openssl s_client -connect hostname:port | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /tmp/serveur.cert

```

## Tips

### Récupérer les sorties standarts de plusieurs commandes

```bash
$ false | true
$ echo "${PIPESTATUS[0]} ${PIPESTATUS[1]}"
1 0
```

### Définir une valeur par defaut pour une variable

```bash
LOG_LEVEL=${LOG_LEVEL:-"INFO"}
```

### Vérifier si une variable est définie

Se mécanisme se base sur l expansion des paramètres. Ici si le paramètre est déclaré, on retrourne x sinon la chaîne de caractères vide.

```bash
$ if [ -z ${JAVA_HOME+x} ]; then echo "JAVA_HOME is unset"; else echo "JAVA_HOME is set to '$JAVA_HOME'"; fi
var is set to 'D:\tools\java\jdk1.8.0_25'
```

### Trouver une classe dans une liste de jar

```bash
for i in `find . -name \*.jar`; do jar tvf $i|grep -qi <MA_CLASSE> && echo $i ; done
```

### Lister les serialUID des jar

```bash
for i in `find . -name \*.jar`; do echo "Jar : $i"; for j in `jar tf $i | grep class | cut -d"." -f1 | sed -e 's/\//./g'` ; do serialver -classpath $i $j| grep fr ; done; done
```

### Liens utiles

- [Matrice commandes unix par OS](http://www.datadisk.co.uk/html_docs/misc/unix_commands.htm)
- [Cle ssh](http://www.linux-france.org/prj/edu/archinet/systeme/ch13s03.html)
