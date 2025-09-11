---
{"dg-publish":true,"permalink":"/가.오라클/MAA/AFFIRM and NOAFFIRM/","dgPassFrontmatter":true,"noteIcon":""}
---

#### CreateDate : 2025-09-11

### GOAL
(active) datagaurd / ogg / downstream  환경에서 sync / async + affirm / noaffirm 조합 조사
### Tested Env, APPLIES TO:
Oracle Enterprise Linux 8.10
Oracle Database - Enterprise Edition - Version 19c - 23ai

### SOLUTION

- affirm / noaffirm 의 차이는 standby database 의 standby redo log 에 데이터를 쓰기 완료하였는지 확인하는 로직임
- 즉, async + affirm 조합은 syntax내에서 허용되나 큰 의미는 없어보이며, 추후 버전에서는 desupport 될 예정(deprecated and will not be supported in future releases.)
- 다만 내부 lab 에서는 제한된 환경에서 async + affirm 조합을 사용 확인함.
	- case 1) adg 의 내용을 다시 cascade 구성하여 downstream으로 받아야 하는 경우.
	- case 2) seamless role change가 필요한 경우.


- 19c / 23ai  문서내용 동일
https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/LOG_ARCHIVE_DEST_n-parameter-attributes.html#GUID-559248E6-B153-4B7B-A8A9-9CEE102088F0
https://docs.oracle.com/en/database/oracle/oracle-database/23/sbydb/LOG_ARCHIVE_DEST_n-parameter-attributes.html#SBYDB01101
#### AFFIRM and NOAFFIRM
The `AFFIRM` and `NOAFFIRM` attributes control whether a redo transport destination acknowledges received redo data before or after writing it to the standby redo log.
Definitions of each option are as follows:
- `AFFIRM`—specifies that a redo transport destination acknowledges received redo data after writing it to the standby redo log.
- `NOAFFIRM`—specifies that a redo transport destination acknowledges received redo data before writing it to the standby redo log.

| Category                  | AFFIRM                                       | NOAFFIRM                                     |
| :------------------------ | :------------------------------------------- | :------------------------------------------- |
| Data type                 | Keyword                                      | Keyword                                      |
| Valid values              | Not applicable                               | Not applicable                               |
| Default Value             | Not applicable                               | Not applicable                               |
| Requires attributes       | `SERVICE`                                    | `SERVICE`                                    |
| Conflicts with attributes | `NOAFFIRM`                                   | `AFFIRM`                                     |
| Corresponds to            | `AFFIRM` column of the `V$ARCHIVE_DEST` view | `AFFIRM` column of the `V$ARCHIVE_DEST` view |

Usage Notes
- If neither the `AFFIRM` nor the `NOAFFIRM` attribute is specified, then the default is `AFFIRM` when the `SYNC` attribute is specified and `NOAFFIRM` when the `ASYNC` attribute is specified.
- Specification of the `AFFIRM` attribute without the `SYNC` attribute is deprecated and will not be supported in future releases.

### Conclusion
일반적인 환경에서는 sync + affirm / async + noaffirm 으로 구성해서 사용하면 됨.

### REFERENCES
https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/LOG_ARCHIVE_DEST_n-parameter-attributes.html#GUID-559248E6-B153-4B7B-A8A9-9CEE102088F0
https://docs.oracle.com/en/database/oracle/oracle-database/23/sbydb/LOG_ARCHIVE_DEST_n-parameter-attributes.html#SBYDB01101

https://docs.oracle.com/en/middleware/goldengate/core/19.1/oracle-db/handling-role-changes-adg-configuration.html
Configure redo transport in a Golden Gate configuration with Data Guard (Doc ID 2439515.1)
NOTE:1436913.1 - Best Practice - Oracle GoldenGate 11gr2 Integrated Extract and Oracle Data Guard - Switchover/Fail-over Operations
https://www.ateam-oracle.com/post/oracle-goldengate-best-practices-goldengate-capture-from-a-dataguard-with-cascaded-redo-logs