# Tool Box Oracle SQLPlus Commande SQL PL/SQL

<!-- MarkdownTOC depth=3 autolink=true bracket=round -->

- [SQLPlus](#sqlplus)
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
    - [Scripts](#scripts)
- [PL/SQL](#plsql)
    - [Gestion des erreurs](#gestion-des-erreurs)
- [Unix commande](#unix-commande)
    - [Vérifier que les bases sont bien démarrées](#vérifier-que-les-bases-sont-bien-démarrées)

<!-- /MarkdownTOC -->

# SQLPlus

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
imp login/password@service fromuser=test1 touser=test1 file=mon-dump.dmp log=logs-import.log
```

# Commande Sql

### Tablespace

Taille Allouée / Occupée / Libre sur tous les tablespaces.

```sql
SELECT A.tablespace_Name, A.Alloue, B.Occupe, C.Libre
FROM 
  (select tablespace_name, sum(bytes)/1024/1024 AS ALLOUE from dba_data_files group by tablespace_name) a,
  (select tablespace_name, Sum(bytes)/1024/1024 AS OCCUPE from dba_segments group by tablespace_name) b,
  (select tablespace_name, Sum(bytes)/1024/1024 AS LIBRE from dba_free_space group by tablespace_name) c
WHERE B.tablespace_Name = A.tablespace_Name
AND C.Tablespace_Name = B.Tablespace_Name;
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
