---
{"dg-publish":true,"permalink":"/5.Oracle/23ai Test List/","dgPassFrontmatter":true,"noteIcon":""}
---

- acfs 를 이용한 nfs 구성 [[9.일지/2024/2024-01-09\|2024-01-09]]
   - how to mount an acfs filesystem through NFS ON linux (Doc ID 1522878.1) 
- compress for oltp 동작 (in same tablespace) [[9.일지/2024/2024-01-08\|2024-01-08]] -> [[9.일지/2024/2024-01-11\|2024-01-11]]
- virtual box on RAC using freenas [[9.일지/2024/2024-01-01\|2024-01-01]]
- sharding 23c configure Env [[가.오라클/ETC/sharding configuration\|sharding configuration]] [[9.일지/2024/2024-01-16\|2024-01-16]] [[9.일지/2024/2024-01-17\|2024-01-17]] [[9.일지/2024/2024-01-19\|2024-01-19]] [[9.일지/2024/2024-01-20\|2024-01-20]] [[9.일지/2024/2024-01-22\|2024-01-22]]
- multitenant 
	- listener / timezone [[9.일지/2025/2025-07-31\|2025-07-31]]
- 23ai 
	- dbca / help contents [[9.일지/2025/2025-07-30\|2025-07-30]]
	- sga test / [[가.오라클/Memory/CDB sga test 23ai\|CDB sga test 23ai]]
	- patch / [[가.오라클/patch/Two-Stage Rolling Updates\|Two-Stage Rolling Updates]] [[가.오라클/patch/About Local Rolling Database Maintenance\|About Local Rolling Database Maintenance]]

```
dbca -silent -createDatabase -templateName seed_db.dbc -gdbname PRICDB -sid PRICDB -responseFile NO_VALUE -characterSet AL32UTF8 -sysPassword manager -systemPassword manager -createAsContainerDatabase true -numberOfPDBs 0 -pdbName ORCL -pdbAdminPassword manager -databaseType MULTIPURPOSE -memoryMgmtType auto_sga -totalMemory 20240 -storageType ASM -datafileDestination "+DATA" -redoLogFileSize 200 -emConfiguration NONE -initParams _exadata_feature_on=true,db_create_file_dest=+DATA,db_create_online_log_dest_1=+DATA -nodelist oranode1,oranode2 -ignorePreReqs 

dbca -silent -createDatabase -templateName seed_db.dbc -gdbname SING2 -sid SING2 -responseFile NO_VALUE -characterSet AL32UTF8 -sysPassword manager -systemPassword manager -createAsContainerDatabase true -numberOfPDBs 0 -pdbName ORCL -pdbAdminPassword manager -databaseType MULTIPURPOSE -memoryMgmtType auto_sga -totalMemory 20240 -storageType ASM -datafileDestination "/u02/oradata" -redoLogFileSize 200 -emConfiguration NONE -initParams _exadata_feature_on=true,db_create_file_dest=/u02/oradata,db_create_online_log_dest_1=/u02/oradata -nodelist oranode1,oranode2 -ignorePreReqs -useOMF true

CREATE PLUGGABLE DATABASE ORCL
ADMIN USER pdbadmin IDENTIFIED BY manager;
```


---

#oracle #poc #23ai 