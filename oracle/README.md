# Tool Box Oracle SQLPlus Commande SQL PL/SQL

<!-- MarkdownTOC depth=3 autolink=true bracket=round -->

- [SQLPlus](#sqlplus)
  - [Installation de SQLPlus](#installation-de-sqlplus)
  - [Configuration de SQLPlus](#configuration-de-sqlplus)
  - [Se connecter à une BD](#se-connecter-à-une-bd)
  - [Lancer un script SQL via SQLPlus](#lancer-un-script-sql-via-sqlplus)
  - [Comande SQLPlus](#comande-sqlplus)
- [Oracle](#oracle)
  - [Export d'un dump oracle](#export-dun-dump-oracle)
  - [Import d'un dump oracle](#import-dun-dump-oracle)
- [Commande Sql](#commande-sql)
  - [Tablespace](#tablespace)
  - [Tables](#tables)
  - [Users](#users)
  - [Variables](#variables)
  - [dblink](#dblink)
  - [Scripts](#scripts)
- [PL/SQL](#plsql)
  - [Gestion des erreurs](#gestion-des-erreurs)
- [Unix commande](#unix-commande)
  - [Vérifier que les bases sont bien démarrées](#vérifier-que-les-bases-sont-bien-démarrées)

<!-- /MarkdownTOC -->

# SQLPlus

### Installation de SQLPlus

Télécharger les binaires ci-dessous depuis le site d'oracle : http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html

* Instant Client Package - Basic Lite
* Instant Client Package - SQL*Plus

**Mode opératoire **
```sh
mkdir -p /opt/oracle/product/
cd /opt/oracle/product/
unzip /tmp/basiclite-10.2.0.4.0-linux-x86_64.zip
unzip /tmp/sqlplus-10.2.0.4.0-linux-x86_64.zip
cd instantclient_10_2
ln -s libclntsh.so.10.1 libclntsh.so
ln -s libocci.so.10.1 libocci.so

# Potionnement des variables
export LD_LIBRARY_PATH=/opt/oracle/product/instantclient_10_2:$LD_LIBRARY_PATH
export PATH=/opt/oracle/product/instantclient_10_2:$PATH
export ORACLE_HOME=/opt/oracle/product/instantclient_10_2
```

### Configuration de SQLPlus 

Il faut définir les variables ci-dessous soit dans l'invite de cmd soit dans le .profile 

```sh
export ORACLE_HOME=/opt/oracle/product/10.2.0/
export ORACLE_BASE=/opt/oracle
export ORACLE_SID=ROSA
```

### Se connecter à une BD
```sh
sqlplus login/password@service

# Sans le fichier tnsname.ora 
sqlplus "login/password@(DESCRIPTION= (ADDRESS= (HOST=<hostname>) (PROTOCOL=TCP) (PORT=<port>)) (CONNECT_DATA= (SID=<sid>)))"
```

### Lancer un script SQL via SQLPlus
Cela permet de lancer un script sql depuis un shell. A noter qu'il faut penser au Exit dans le script.
```sh
sqlplus login/password@service @/tmp/mon_script.sql > ./log-script.log
```

### Comande SQLPlus

Pour agrandir la largeur d'affichage
```sql
SET LINESIZE 300
```

Pour augmenter le nombre de lignes affcihées dans une page
```sql
SET PAGESIZE 1000
```

# Oracle

### Export d'un dump oracle
```sh
exp login/password@service owner=test file=mon-dump.dmp log=logs-export.log
```

### Import d'un dump oracle
```sh
--- Import complet
imp login/password@service fromuser=test1 touser=test1 file=mon-dump.dmp log=logs-import.log

--- Import selectif
imp login/password@service fromuser=test1 touser=test1 file=mon-dump.dmp log=logs-import.log tables=(table1,table2,table3,table4)
```

# Commande Sql

### Tablespace

Taille Allouée / Occupée / Libre sur tous les tablespaces.

```sql
SELECT A.tablespace_Name, A."ALLOUE (en Mo)", B."OCCUPE (en Mo)", C."LIBRE (en Mo)"
FROM 
  (select tablespace_name, sum(bytes)/1024/1024 AS "ALLOUE (en Mo)" from dba_data_files group by tablespace_name) a,
  (select tablespace_name, Sum(bytes)/1024/1024 AS "OCCUPE (en Mo)" from dba_segments group by tablespace_name) b,
  (select tablespace_name, Sum(bytes)/1024/1024 AS "LIBRE (en Mo)" from dba_free_space group by tablespace_name) c
WHERE B.tablespace_Name = A.tablespace_Name
AND C.Tablespace_Name = B.Tablespace_Name;

TABLESPACE_NAME                ALLOUE (en Mo) OCCUPE (en Mo) LIBRE (en Mo)
------------------------------ -------------- -------------- -------------
SYSAUX                                    160         152,25        7,6875
DAT_IO_TABLE                           3500         3053,5      446,4375
UNDOTBS                                   250        10,5625       239,375
IDXTABLE                              24000     22724,6875      1275,125
SYSTEM                                    390       349,9375            40
DATA_MIG                                10000       254,4375        9988,5
DATTABLE                              28000     26724,6875      1275,125
DTSTAGE                                   500         408,25       91,6875

--- Attention : En 10g, le "LIBRE" peut être différent de ("ALLOUE"- "OCCUPE") à cause de la recyclebin ! 
--- Le recyclebin sert a sauvegarder les objet dropé
--- Dans se cas il faut purge le recyclebin
--- Se connecter en sysdba et lancer la commande ci-dessous.
purge dba_recyclebin;

```

Espace libre sur tous les TBS

```sql
select tablespace_name, sum (bytes) / 1024 / 1024 as "Espace libre (en Mo)" from dba_free_space
group by tablespace_name ;
```

Liste des objets sur un TBS
```sql
SELECT
     substr(tablespace_name,1,15)  AS "Nom du Tablespace",
     substr(owner,1,15)  AS "Propriétaire du segment",
     substr(segment_name,1,15) AS "Nom du segment",
     segment_type AS "Type du segment",
     extents                                      AS "Nombre d'extents du segment",
     blocks                                        AS "Nombre de blocks du segment",
     bytes                                          AS "Nombre de bytes du segment"
FROM sys.dba_segments where tablespace_name='INDEX_MIG';
```

Espace utilisé sur le TBS TEMP par session 
```sql
select   se.osuser, se.username, se.sid,
         su.extents, su.blocks * to_number(rtrim(p.value)) as Space,
         tablespace
from     v$sort_usage su, v$parameter p, v$session se
where    p.name = 'db_block_size'
and      su.session_addr = se.saddr
order by se.username, se.sid;
```

Supprimer un tablespace
```sql
DROP TABLESPACE INDEX_MIG INCLUDING CONTENTS AND DATAFILES;
```

### Tables

Calcule la taille d'une table
```sql
select sum(bytes)/1024/1024 "SizeMo" 
from dba_extents 
where segment_name ='ARCHIVE_SCEBFFRC';
```

### Users

Liste les users
```sql
select * from all_users;
DROP USER USER_MIG CASCADE;
ALTER USER USER_MIG ACCOUNT UNLOCK;
```

Ajout les droit en select sur toutes les tables d'un schémas
```sql
BEGIN
   FOR R IN (SELECT owner, table_name FROM all_tables WHERE owner='SOC1') LOOP
      EXECUTE IMMEDIATE 'grant select on '||R.owner||'.'||R.table_name||' to USER_MIG';
   END LOOP;
END;
/
```

### Variables

Lister les variables
```sql
DEF
```

Définir une variable
```sql
DEFINE ma_table = "REGIONS"
```

Appeler une variable dans une requête
```sql
SELECT * FROM &ma_table
```

### dblink

Un dblink est lien entre 2 base.

Création d'un dblink
```sql
CREATE SHARED PUBLIC DATABASE LINK DBLINK_NAME CONNECT TO SYSTEM IDENTIFIED BY SERVICE AUTHENTICATED BY SYSTEM IDENTIFIED BY SERVICE USING '(DESCRIPTION= (ADDRESS= (HOST=hostname) (PROTOCOL=TCP) (PORT=1555)) (CONNECT_DATA= (SID=SERVICE)))';
```

Suppression d'un dblink
```sql
DROP PUBLIC DATABASE LINK DBLINK_NAME;
```

### Scripts

Appeler un script dans un script 
```sql
@./mon-script.sql
```

# PL/SQL

### Gestion des erreurs

```sql
BEGIN
  EXECUTE IMMEDIATE 'DROP TABLE MA_TABLE';
EXCEPTION
WHEN OTHERS THEN
  NULL;
END;
/
```


# Unix commande

### Vérifier que les bases sont bien démarrées

```sh
[oracle@machine1:/] ps -ef | grep pmon
    root  5435  3689  0 16:05:31 pts/2    0:00 grep pmon
  oracle  4422     1  0 16:01:55 ?        0:00 ora_pmon_BD1
  oracle  4622     1  0 16:02:05 ?        0:00 ora_pmon_BD2
  oracle  4818     1  0 16:02:12 ?        0:00 ora_pmon_BD3
  oracle  5020     1  0 16:02:21 ?        0:00 ora_pmon_BD4
```
