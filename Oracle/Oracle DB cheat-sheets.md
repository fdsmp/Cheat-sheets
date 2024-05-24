# Oracle DB cheat-sheets

## Afficher les instances de DB en cours d'exécution sur un serveur Oracle
### Méthode 1 : ps

```bash
ps -ef | grep pmon | grep -v grep
```

> Chaque instance Oracle a un processus `ora_pmon_<SID>`.

Exemple de sortie :

```
oracle   12345     1  0 10:00 ?        00:00:03 ora_pmon_orcl
oracle   12346     1  0 10:00 ?        00:00:03 ora_pmon_testdb
```

### Méthode 2 : oratab

```shell
cat /etc/oratab
```

> Le fichier `oratab`  contient des informations sur les instances configurées sur le serveur.

Exemple de sortie :

```
orcl:/u01/app/oracle/product/19.0.0/dbhome_1:N
testdb:/u01/app/oracle/product/19.0.0/dbhome_1:N
```

### Méthode 3 : lsnrctl

Utilisez la commande `lsnrctl` pour obtenir le statut du listener et les services associés :

```shell
lsnrctl status
```

Exemple de sortie :

```
LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 22-MAY-2024 10:00:00

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=localhost)(PORT=1521)))
STATUS of the LISTENER
------------------------
Alias                     LISTENER
Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
Start Date                22-MAY-2024 09:00:00
Uptime                    0 days 1 hr. 0 min. 0 sec
Trace Level               off
Security                  ON: Local OS Authentication
SNMP                      OFF
Listener Parameter File   /oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
Listener Log File         /oracle/diag/tnslsnr/localhost/listener/alert/log.xml
Listening Endpoints Summary...
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=localhost)(PORT=1521)))
Services Summary...
Service "orcl" has 1 instance(s).
  Instance "orcl", status READY, has 1 handler(s) for this service...
Service "testdb" has 1 instance(s).
  Instance "testdb", status READY, has 1 handler(s) for this service...
```

## SQLPLUS pour interagir avec une instance de DB

Configuration des variables d'environnement `ORACLE_BASE`, `ORACLE_HOME`, et `ORACLE_SID` avec le script `oraenv`:

```shell
. oraenv
```

> - Saisir le SID de la base de données.
> - Le script définit automatiquement les variables `ORACLE_HOME` et `ORACLE_BASE`.

Se connecter avec un utilisateur système et les privilèges `sysdba`

```shell
sqlplus / as sysdba
```

> L'utilisateur système doit être membre du groupe `dba`.
## Obtenir des informations sur l'instance actuelle

```sql
SET LINESIZE 100
COLUMN instance_name FORMAT A15
COLUMN host_name FORMAT A20
COLUMN status FORMAT A10

SELECT instance_name, host_name, status
FROM v$instance;

```

>  Utilisez la commande `describe` ou `DESC` pour  afficher la structure d'une table ou d'une vue.

## Afficher les information des utilisateurs d'une base de données

1. Afficher les Utilisateurs du Dictionnaire de Données(`DBA_USERS`) :

```sql
COLUMN username FORMAT A30
COLUMN account_status FORMAT A15

SELECT username, account_status
FROM dba_users;
```

2. Afficher Utilisateurs Actuels (`ALL_USERS`) :

```sql
COLUMN username FORMAT A30
COLUMN account_status FORMAT A15

SELECT username FROM all_users;
```

3. Info de votre  utilisateur (`USER_USERS`) :

```sql
COLUMN username FORMAT A30
COLUMN account_status FORMAT A15

SELECT username, account_status
FROM user_users;
```

4. Afficher les rôles d'un utilisateur :

```sql
SELECT granted_role
FROM dba_role_privs
WHERE grantee = 'nom_utilisateur';
```

5. Afficher les privilèges système d'un utilisateur :

```sql
SELECT privilege
FROM dba_sys_privs
WHERE grantee = 'nom_utilisateur';
```

6. Afficher les privilèges sur les objets d'un utilisateur :

```sql
SELECT privilege, table_name
FROM dba_tab_privs
WHERE grantee = 'nom_utilisateur';
```

