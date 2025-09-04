---
{"dg-publish":true,"permalink":"/가.오라클/patch/About Local Rolling Database Maintenance/","dgPassFrontmatter":true,"noteIcon":""}
---


#### CreateDate : 2025-09-04

### GOAL
local rolling database 테스트를 위한 시나리오 진행
### Tested Env, APPLIES TO:
Oracle Enterprise Linux 8.10
Oracle Database - Enterprise Edition - Version 23.4 to 23.7 

### SOLUTION
1. filesystem to asm 을 진행하기 위한 loopback device 구성
~~~
mkdir -p /oralabnfs/asmdisks
for i in 1 2 3 4 5; do
  dd if=/dev/zero of=/oralabnfs/asmdisks/asm_disk${i}.file bs=1M count=20480 status=progress conv=fsync
done

modprobe loop
losetup -D   # 모든 기존 루프 해제
for i in 1 2 3 4 5; do
  losetup /dev/loop${i} /oralabnfs/asmdisks/asm_disk${i}.file
done
losetup -a
blockdev --getsize64 /dev/loop1
blockdev --getsize64 /dev/loop2
blockdev --getsize64 /dev/loop3
blockdev --getsize64 /dev/loop4
blockdev --getsize64 /dev/loop5

chown grid:oinstall /dev/loop[1-5]
chmod 660 /dev/loop[1-5]

이후 asmdg 수동생성
asmca -silent -oui_internal -configureASM -param _exadata_feature_on=true -diskString '/dev/loop1,/dev/loop2,/dev/loop3,/dev/loop4,/dev/loop5' -diskGroupName DATA -diskList '/dev/loop1,/dev/loop2,/dev/loop3,/dev/loop4,/dev/loop5' -redundancy EXTERNAL -au_size 4

log)
ASM has been created and started successfully.
[DBT-30001] Disk groups created successfully. Check /u02/app/oracle/cfgtoollogs/asmca/asmca-250904AM110943.log for details.
~~~

2. oraclehome switch
   $ srvctl modify database -db CDBORCL -oraclehome /u01/app/oracle/product/23.7/dbhome_1
	~~~
	Connected to:
	Oracle Database 23ai Enterprise Edition Release 23.0.0.0.0 - for Oracle Cloud and Engineered Systems
	Version 23.7.0.25.01
	
	SQL> alter database open;
	alter database open
	*
	ERROR at line 1:
	ORA-00603: ORACLE server session terminated by irrecoverable error
	ORA-01092: ORACLE instance terminated. Disconnection forced
	ORA-00604: Error occurred at recursive SQL level 1. Check subsequent errors.
	ORA-00904: "SPARE12": invalid identifier
	Process ID: 2413206
	Session ID: 4 Serial number: 6581
	Help: https://docs.oracle.com/error-help/db/ora-00603/
	~~~

- alert log
~~~
2025-09-04T15:53:55.915756+09:00  
Errors in file /u01/app/diag/rdbms/cdborcl/CDBORCL/trace/CDBORCL_ora_2411870.trc:  
ORA-00604: Error occurred at recursive SQL level 1. Check subsequent errors.  
ORA-00904: "SPARE12": invalid identifier  
2025-09-04T15:53:55.915822+09:00  
Errors in file /u01/app/diag/rdbms/cdborcl/CDBORCL/trace/CDBORCL_ora_2411870.trc:  
ORA-00604: Error occurred at recursive SQL level 1. Check subsequent errors.  
ORA-00904: "SPARE12": invalid identifier  
Error 604 happened during db open, shutting down database  
2025-09-04T15:53:56.122951+09:00  
Errors in file /u01/app/diag/rdbms/cdborcl/CDBORCL/trace/CDBORCL_ora_2411870.trc (incident=24492) (PDBNAME=CDB$ROOT):  
ORA-00603: ORACLE server session terminated by irrecoverable error  
ORA-01092: ORACLE instance terminated. Disconnection forced  
ORA-00604: Error occurred at recursive SQL level 1. Check subsequent errors.  
ORA-00904: "SPARE12": invalid identifier  
Incident details in: /u01/app/diag/rdbms/cdborcl/CDBORCL/incident/incdir_24492/CDBORCL_ora_2411870_i24492.trc  
2025-09-04T15:53:58.400085+09:00  
Errors in file /u01/app/diag/rdbms/cdborcl/CDBORCL/trace/CDBORCL_ora_2411993.trc (incident=24524) (PDBNAME=CDB$ROOT):  
ORA-06544: PL/SQL: internal error, arguments: [pht_init_hash:lookup], [25], [], [], [], [], [], []  
ORA-06553: PLS-801: internal error [pht_init_hash:lookup]  
Incident details in: /u01/app/diag/rdbms/cdborcl/CDBORCL/incident/incdir_24524/CDBORCL_ora_2411993_i24524.trc  
2025-09-04T15:53:59.290283+09:00  
opiodr aborting process unknown ospid (2411870) service_name =oraclecdborcl as a result of ORA-603  
2025-09-04T15:53:59.294630+09:00  
ORA-603 : opitsk aborting process  
License high water mark = 4  
--ATTENTION--  
USER(prelim) (ospid: 2411870): terminating the instance due to ORA error 604  
2025-09-04T15:53:59.384859+09:00  
ORA-1092 : opitsk aborting process  
2025-09-04T15:53:59.393180+09:00  
ORA-1092 : opitsk aborting process  
Instance terminated by USER(prelim), PID = 2411870
~~~

### Conclusion
dictionary 관련 정합성 에러 발생되며, open 이 불가. datapatch 수행불가 

### REFERENCES
https://docs.oracle.com/en/database/oracle/oracle-database/23/racad/administering-database-instances-and-cluster-databases.html#GUID-E862ADCB-389C-4C43-8EC5-3F2DE5F0FBCE

#patch #23ai #switchhome 