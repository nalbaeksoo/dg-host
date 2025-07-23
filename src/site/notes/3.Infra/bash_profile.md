---
{"dg-publish":true,"permalink":"/3.Infra/bash_profile/","dgPassFrontmatter":true,"noteIcon":""}
---


```
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
export TMP=/tmp
export TMPDIR=$TMP

export ORACLE_BASE=/u02/app/oracle
export ORACLE_HOME=$ORACLE_BASE/product/23c/dbhome_1
export ORA_INVENTORY=/u02/app/oraInventory
export ORACLE_SID=PRIS
export ORACLE_PDB_SID=ACUP
export INST_ID=1
#export DATA_DIR=/u01/oradata

export ORACLE_UNQNAME=PRIS
export ORACLE_UNQNAME_LOWER=`echo $ORACLE_UNQNAME | tr A-Z a-z`
export ORACLE_SID=${ORACLE_UNQNAME}$INST_ID
export PATH=$PATH:$ORACLE_HOME/bin:$ORACLE_HOME/OPatch
#export TNS_ADMIN=$ORACLE_HOME/network/admin
#export NLS_LANG=American_America.KO16KSC5601

export PATH=/usr/sbin:/usr/local/bin:$ORACLE_HOME/bin:$ORACLE_HOME/OPatch:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib

export DIAGNOSTIC_DEST='/u01/app'
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib
export PS1="[\u@\h \D{%m%d-%H:%M:%S}]:\$ORACLE_SID:\$ORACLE_PDB_SID:[\$PWD]
$ "
alias unpdb='unset ORACLE_PDB_SID'
alias sd='sqlplus / as sysdba'
alias bdump='cd $DIAGNOSTIC_DEST/diag/rdbms/${ORACLE_UNQNAME_LOWER}/${ORACLE_SID}/trace'
alias t_dbalert='tail -30f $DIAGNOSTIC_DEST/diag/rdbms/${ORACLE_UNQNAME_LOWER}/${ORACLE_SID}/trace/alert*.log'
alias oratop='$ORACLE_HOME/suptools/oratop/oratop -r / as sysdba'
```