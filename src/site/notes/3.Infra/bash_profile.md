---
{"dg-publish":true,"permalink":"/3.Infra/bash_profile/","dgPassFrontmatter":true,"noteIcon":""}
---


```
export TMP=/tmp
export TMPDIR=$TMP

export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/23c/dbhome_1
export ORA_INVENTORY=/u01/app/oraInventory
export ORACLE_SID=CDB1
export ORACLE_PDB_SID=PDBSHA2
export DATA_DIR=/u02/oradata

export PATH=/usr/sbin:/usr/local/bin:$PATH
export PATH=$ORACLE_HOME/bin:$PATH

export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib

export PS1="[\u@\h \D{%Y%m%d-%H:%M:%S}] 
[\$PWD]
$ "
alias uspdb='unset ORACLE_PDB_SID'
```