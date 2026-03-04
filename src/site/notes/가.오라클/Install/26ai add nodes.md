---
{"dg-publish":true,"permalink":"/가.오라클/Install/26ai add nodes/","dgPassFrontmatter":true,"noteIcon":""}
---

#### CreateDate : 2026-03-04

### GOAL
oracle database 26ai 에서 add nodes 수행 2 node -> 5 node 확장
### Tested Env, APPLIES TO:
Oracle Enterprise Linux 8.10
Oracle Database - Enterprise Edition - Version 26ai

### SOLUTION

RAC의 기본적인 이해도를 전재함.

1. Prerequisite Steps for Adding Cluster Nodes 기본 점검사항 확인
	- Check Network Requirements
	- Verify Kernel Parameters
	- Set Up Shared Storage
	- Check Account Configuration
	- Configure Secure Shell on All Cluster Nodes
``` user_id
5-7 번 노드 동일
[oracle@oranode5 20260303-21:50:11]:SOETC1:[/home/oracle]
$ id
uid=54321(oracle) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54323(oper),54324(backupdba),54325(dgdba),54326(kmdba),54330(racdba) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
[grid@oranode5 20260303-21:50:44]::[/home/grid]
$ id
uid=54322(grid) gid=54321(oinstall) groups=54321(oinstall) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```
2. /etc/oraInst.loc 파일을 5-7노드에 배포 
3. 각 노드의 ssh (equiv) 상태 확인
4. 기존 clusterware 에서 grid user로 아래 명령어 수행
```
/u01/app/oracle/product/26ai/gridhome_1/runcluvfy.sh stage -pre nodeadd -n oranode5

Initializing ...

Performing following verification checks ...

  Physical Memory ...PASSED
  Available Physical Memory ...PASSED
  Swap Size ...PASSED
  Free Space: oranode1:/usr,oranode1:/var,oranode1:/etc,oranode1:/u01/app/oracle/product/26ai/gridhome_1,oranode1:/sbin,oranode1:/tmp ...PASSED
  Free Space: oranode5:/usr,oranode5:/var,oranode5:/etc,oranode5:/u01/app/oracle/product/26ai/gridhome_1,oranode5:/sbin,oranode5:/tmp ...PASSED
  User Existence: oracle ...
    Users With S기

### REFERENCES
Adding or Deleting Oracle RAC Nodes for Oracle E-Business Suite Release 12.2 with Oracle AI Database 26ai - KA1245



