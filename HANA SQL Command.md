###  HANA SQL commands

```sql

--HDB Connect

hdbsql -i 00 -u SYSTEM -p Wedv84u$ -n sapvhgwxdb:30013  
hdbsql -i 00 -u SAPECC -p D$n314Zz$ECX -n sapvhecxdb:30041

--Tenant creation
CREATE DATABASE GWQ SYSTEM USER PASSWORD Wedv84u$;
ALTER SYSTEM STOP DATABASE <database_name>

--User creation
ALTER USER SYSTEM ACTIVATE USER NOW;  
ALTER USER A_LOCKED_USER RESET CONNECT ATTEMPTS;

--Manual backup restore
backup data for S00 using file ('COMPLETE_DATA_BACKUP')
backup data using file ('COMPLETE_DATA_BACKUP')


hdbsql -i 00 -u SYSTEM -p Huawei123 -n localhost:30013 "backup data using file ('COMPLETE_DATA_BACKUP')"

hdbsql -i 00 -u SYSTEM -p Wedv84u$ -n sapvhbdrdb1:30013 "backup data for BDR using file ('COMPLETE_DATA_BACKUP')"
hdbsql -i 00 -u SYSTEM -p Wedv84u$ -n sapvhbdrdb1:30013 "backup data using file ('COMPLETE_DATA_BACKUP')"

RECOVER DATA FOR GWQ  USING FILE ('/backup/DB_GWQ/C_SysCopy_DATA_BACKUP')  CLEAR LOG
RECOVER DATA FOR SCX  USING FILE ('/backup/DB_SCX/DB_SCX/SysCopy_SCX')  CLEAR LOG

--Verify ports

SELECT DATABASE_NAME, SQL_PORT FROM SYS_DATABASES.M_SERVICES 

-- 1. create snapshot
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'GWX HA t-snapshot'

-- 1a. create snapshot verify
SELECT * FROM M_BACKUP_CATALOG WHERE ENTRY_TYPE_NAME = 'data snapshot'
SELECT * FROM M_BACKUP_CATALOG WHERE ENTRY_TYPE_NAME = 'data snapshot' and ENTRY_ID ='1622225278697'
	
SELECT ENTRY_ID FROM M_BACKUP_CATALOG 
 WHERE ENTRY_TYPE_NAME = 'data snapshot'
    AND COMMENT = 'GWD HA t-snapshot'
    
SELECT ENTRY_ID FROM M_BACKUP_CATALOG WHERE ENTRY_TYPE_NAME = 'data snapshot' AND STATE_NAME = 'prepared'

--2 close snapshot succesful     
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 1610741698464 SUCCESSFUL 'SNAP_01'

-- 2a or discard it 
BACKUP DATA FOR FULL SYSTEM 
  CLOSE SNAPSHOT BACKUP_ID 1495207307659
  UNSUCCESSFUL 'Do not use - was manually terminated';

SYSTEMDB, no indexserver , but nameserver sql port
TENENTDB-default , indexserver internal port OS LEVEL 	external SQL port
TENENTDB-SID , indexserver internal port OS LEVEL 	external SQL port

CREATE TABLE FULLNAME (FNAME CHAR(20), LNAME CHAR (20) )
INSERT INTO FULLNAME (FNAME, LNAME) VALUES ('hana', 'montana')
SELECT * FROM FULLNAME

ALTER SYSTEM CANCEL SESSION 'xxxxx';
select count (*) from SAPGRC.USR02;

hdblogdiag directory --recreate /hana/log/BDR/mnt00001/hdb00001/
hdblogdiag directory --recreate /hana/log/HDQ/mnt00001/hdb00002.00004/
hdblogdiag directory --recreate //hana/data/BDR/mnt00001/hdb00003.00003/
hdbbackupdiag --check --logDirs /backup/DONTDELETE_LOG_BACKUP/DB_GWP/ --dataDir /backup/DB13_BACKUP/DB_GWP/


RECOVER DATABASE FOR BDR UNTIL TIMESTAMP '2021-07-14 17:50:51' USING CATALOG PATH ('/usr/sap/BDR/HDB00/backup/log/DB_BDR') USING LOG PATH ('/usr/sap/BDR/HDB00/backup/log/DB_BDR','/usr/sap/BDR/HDB00/backup/log/DB_BDR') USING DATA PATH ('/backup/data/DB_BDR/') USING BACKUP_ID 1594703216671 CHECK ACCESS USING FILE
	
	





select plan_id from M_SQL_PLAN_CACHE where statement_hash = '8a1794d61989ca967758181dcb8b3ea2'
SELECT * FROM STATEMENT_HINTS
ALTER SYSTEM PIN SQL PLAN CACHE ENTRY 154460002 WITH HINT(NO_USE_OLAP_PLAN)
ALTER SYSTEM UNPIN SQL PLAN CACHE ENTRY '154460002';

ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini','SYSTEM') SET ('parallel','tables_preloaded_in_parallel') = '18' WITH RECONFIGURE;


sysctl -a| grep -E "net.core.somaxconn|net.ipv4.ip_local_port_range|net.ipv4.tcp_max_syn_backlog|net.ipv4.tcp_syn_retries|net.ipv4.tcp_timestamps|net.ipv4.tcp_tw_recycle|net.ipv4.tcp_rmem|net.core.rmem_max|net.ipv4.tcp_wmem|net.core.wmem_max"
sysctl -a| grep -E "net.core.somaxconn|net.ipv4.tcp_max_syn_backlog|net.core.rmem_max|net.core.wmem_max"

Adjust Network parameters at OS level

echo 4096 > /proc/sys/net/core/somaxconn
or
sysctl -w net.core.somaxconn=4096

echo 8192 > /proc/sys/net/ipv4/tcp_max_syn_backlog
or
sysctl -w net.ipv4.tcp_max_syn_backlog=8192

echo 12582912 > /proc/sys/net/core/rmem_max
echo 12582912 > /proc/sys/net/core/wmem_max
or
sysctl -w net.core.rmem_max=12582912
sysctl -w net.core.wmem_max=12582912

vi /etc/sysctl.conf add below parameters for changes to be persistent
net.core.somaxconn=4096
net.ipv4.tcp_max_syn_backlog=8192
net.core.rmem_max=12582912
net.core.wmem_max=12582912

SYSTEMDB Parameters
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('memorymanager','statement_memory_limit') = '500' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('nameserver.ini','SYSTEM') SET ('sql','plan_cache_eager_eviction_mode') = 'off' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('preprocessor.ini','SYSTEM') SET ('jobqueue','num_cores') = '55' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('communication','tcp_backlog') = '4096' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('inifile_checker','replicate') = 'true' WITH RECONFIGURE;

Tenant DB Parameters

ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('persistence','logreplay_savepoint_interval_s') = '900' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('resource_tracking','service_thread_sampling_monitor_enabled') = 'true' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('system_replication','logshipping_max_retention_size') = '327680' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('joins','single_thread_execution_for_partitioned_tables') = 'false' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM' ) SET ('memorymanager', 'huge_alignment_gc') = 'false', ('memorymanager', 'huge_alignment_cache_target') = '10240' WITH RECONFIGURE COMMENT 'SAP Note 2953186.';﻿
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini','SYSTEM') SET ('optimize_compression','row_order_optimizer_threads') = '40' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini','SYSTEM') SET ('parallel','tables_preloaded_in_parallel') = '18' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM' ) SET ('search', 'non_eq_itab_enabled') = 'false' WITH RECONFIGURE COMMENT 'SAP Note 2875426';
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini','SYSTEM') SET ('sql','fail_on_invalid_hint') = 'false' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM') SET ('sql', 'hex_enable_remote_table_access') = 'false' WITH RECONFIGURE COMMENT 'SAP Note 2879457';
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM') SET ('sql', 'hex_min_key_parts') = '5' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini','SYSTEM') SET ('sql', 'plan_cache_eager_eviction_mode') = 'off' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM') SET ('unifiedtable', 'ut_delta_rollover_switch_values') = '3' WITH RECONFIGURE COMMENT 'SAP Note 2940750';
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM') SET ('hex', 'enable_interpreter_cache') = 'false' WITH RECONFIGURE COMMENT 'SAP Note 2808956.';
ALTER SYSTEM ALTER CONFIGURATION ('global.ini','SYSTEM') SET ('inifile_checker','replicate') = 'true' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM') SET ('global', 'timezone_default_data_schema_name') = 'SAPECC' WITH RECONFIGURE;
ALTER SYSTEM ALTER CONFIGURATION ('indexserver.ini', 'SYSTEM') SET ('global', 'timezone_default_data_client_name') = '100' WITH RECONFIGURE;
```





##### HDBSQL

```sql
hdbsql -i 00 -u <user> -p <password> -n <host>:<indexserverport>
hdbsql -i 00 -u SAPECC -p D$n314Zz$ECD -n sapvhecddb:30041

```

### Basic Commands

```sql
SELECT * FROM "SYS"."SCHEMAS";
select * from "SYS"."M_DATABASES"
```

### hdbuserstore Commands

```sql
# HANA Cleaner 

update backup user with system & object priv

hdbuserstore SET <KEY> <host:port> <USERNAME> <PASSWORD>
hdbuserstore set <tagconn-CLNR_BACKUP_USER> SAPVHGWQDB:30013 BACKUP_USER Wedv84u$
hdbuserstore set <tagconn-CLNSYSDB> SAPVHBDRDB1:30013 SYSTEM Wedv84u$
hdbuserstore set DEFAULT SAPVHECPDB1:30013 SAPECC D$n314Zz$ECP ECP

hdbuserstore SET <KEY> <host:port@tenant_database> <USERNAME> <PASSWORD>
hdbuserstore -u leprino-local\ECPadm Set DEFAULT SAPVHECPDB1:30013@ECP SAPECC D$n314Zz$ECP
hdbuserstore -u leprino-local\SAPServiceECP Set DEFAULT SAPVHECPDB1:30013@ECP SAPECC D$n314Zz$ECP


# HANA Cleaner command :
python /usr/sap/HDQ/HDB00/exe/python_support/hanacleaner.py -bd 20 -br true -bb true -k CLNR_BACKUP_USER -op /backup/TEST_DIR
```



### Reset Users

```sql
#Reset the SYSTEM User Password of a Tenant Database
ALTER SYSTEM STOP DATABASE <database_name>
ALTER DATABASE <database_name> SYSTEM USER PASSWORD <new_password>

#Reset the SYSTEM User Password of the System Database
/usr/sap/<SID>/HDB<instance>/hdbenv.sh
/usr/sap/<SID>/HDB<instance>/exe/hdbnameserver -resetUserSystem
```

### Disable Password

```sql
ALTER DATABASE <SID> <USER> USER PASSWORD <password>
ALTER USER <technical-user-name> DISABLE PASSWORD LIFETIME
```

### Activate User(s)

```sql
Select USER_NAME ,
       VALID_FROM,
       VALID_UNTIL,
	   INVALID_CONNECT_ATTEMPTS,
	   PASSWORD_CHANGE_NEEDED,
	   USER_DEACTIVATED,
	   IS_RESTRICTED
from "SYS"."USERS" WHERE USER_DEACTIVATED='TRUE';

ALTER USER <A_LOCKED_USER> RESET CONNECT ATTEMPTS;
ALTER USER SYSTEM ACTIVATE USER NOW;  
ALTER USER <user> ACTIVATE USER NOW; 

DROP USER <backup_user> CASCADE;
CREATE USER <backup_user> PASSWORD <password> NO FORCE_FIRST_PASSWORD_CHANGE;
COMMIT;
GRANT BACKUP ADMIN, DATABASE BACKUP ADMIN, DATABASE RECOVERY OPERATOR, CATALOG READ, INIFILE ADMIN, DATABASE START, DATABASE STOP, TRACE ADMIN, SERVICE ADMIN TO <backup_user>
GRANT BACKUP ADMIN, DATABASE BACKUP ADMIN, CATALOG READ, INIFILE ADMIN TO <backup_user>;
```

###  STOP & Drop Databases:

```sql
-- DROP TENANT DATABASE
ALTER SYSTEM STOP DATABASE <SID>
DROP DATABASE <SID>

ALTER SYSTEM STOP DATABASE <database_name> [ IMMEDIATE [ WITH COREFILE ] ]
DROP DATABASE <database_name> [ DROP BACKUPS ]

```



AUTO MERGE

CON



