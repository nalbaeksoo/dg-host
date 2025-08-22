---
{"dg-publish":true,"permalink":"/3.Infra/Swingbench/","dgPassFrontmatter":true,"noteIcon":""}
---


### download media
```
curl -L -o swingbench25052023.zip https://github.com/domgiles/swingbench-public/releases/download/production/swingbench25092024.zip
curl -L -o jdk-23_linux-x64_bin.rpm https://download.oracle.com/java/23/latest/jdk-23_linux-x64_bin.rpm

jdk site : https://www.oracle.com/java/technologies/downloads/
```
### create dummy data

```
./oewizard -cl -create -cs //192.168.100.111:1521/PSI -u soe -p soe -scale 10 -tc 1 -dba "sys as sysdba" -dbap soe -ts SOE -df /u02/oradata/soe.dbf -allindexes -dt thin -part -hashpart -rangepart
./shwizard -cl -create -cs //192.168.100.111:1521/PSI -u sh -p sh -scale 10 -tc 1 -dba "sys as sysdba" -dbap soe -ts SHTBS -df /u02/oradata/sh.dbf -allindexes -dt thin -rangepart
```


### workload play

```

./tpcdswizard -cl -create -cs //192.168.100.111:1521/PSI -u tcpd -p tcpd -scale 5 -tc 1 -dba "sys as sysdba" -dbap soe -ts TCPD -df /u02/oradata/tcpd.dbf -its TPCDSLikeIndexes /u02/oradata/tcpd_idx.dbf -allindexes -dt thin -v

./tpchwizard -cl -create -cs //192.168.100.111:1521/PSI -u tcph -p tcph -scale 5 -tc 1 -dba "sys as sysdba" -dbap soe -ts TCPH -df /u02/oradata/tcph.dbf -allindexes -dt thin -v

./moviewizard -cl -create -cs //192.168.100.111:1521/PSI -u move -p move -scale 1 -tc 1 -dba "sys as sysdba" -dbap soe -ts MOV -df /u02/oradata/move.dbf -allindexes -dt thin -v

./jsonwizard -cl -create -cs //192.168.100.111:1521/PSI -u json -p json -scale 1 -tc 1 -dba "sys as sysdba" -dbap soe -ts JSON -df /u02/oradata/json.dbf -allindexes -dt thin -v


```

### dummy data log
```
Data Generation Runtime Metrics / tpch
+-------------------------+-------------+
| Description             | Value       |
+-------------------------+-------------+
| Connection Time         | 0:00:00.002 |
| Data Generation Time    | 0:06:10.635 |
| DDL Creation Time       | 0:02:28.929 |
| Total Run Time          | 0:08:39.568 |
| Rows Inserted per sec   | 116,827     |
| Actual Rows Generated   | 43,300,000  |
| Commits Completed       | 2,183       |
| Batch Updates Completed | 216,518     |
+-------------------------+-------------+

Validation Report                                                                       
The schema appears to have been created successfully.

Valid Objects
Valid Tables : 'NATION','REGION','PART','SUPPLIER','PARTSUPP','CUSTOMER','ORDERS','LINEITEM'
Valid Indexes : 'ORDERS_PK_IDX','CUST_PK_IDX'
Valid Views : 
Valid Sequences : 'ORDERS_SEQ','CUSTOMER_SEQ'
Valid Code : 
Schema Created

Data Generation Runtime Metrics / tpcd
+-------------------------+-------------+
| Description             | Value       |
+-------------------------+-------------+
| Connection Time         | 0:00:00.001 |
| Data Generation Time    | 0:17:00.795 |
| DDL Creation Time       | 0:08:43.465 |
| Total Run Time          | 0:25:44.267 |
| Rows Inserted per sec   | 135,807     |
| Actual Rows Generated   | 138,471,889 |
| Commits Completed       | 6,986       |
| Batch Updates Completed | 693,210     |
+-------------------------+-------------+

Validation Report                                                                       
The creation of the schema appears to have been unsuccessful.See the following sections for further details.

Valid Objects
Valid Tables : 'CALL_CENTER','CATALOG_PAGE','CATALOG_RETURNS','CATALOG_SALES','CUSTOMER','CUSTOMER_ADDRESS','CUSTOMER_DEMOGRAPHICS','DATE_DIM','HOUSEHOLD_DEMOGRAPHICS','INCOME_BAND','INVENTORY','ITEM','PROMOTION','REASON','SHIP_MODE','STORE','STORE_RETURNS','STORE_SALES','TIME_DIM','WAREHOUSE','WEB_PAGE','WEB_RETURNS','WEB_SALES'
Valid Indexes : 'INVENTORY_NUK'
Valid Views : 
Valid Sequences : 
Valid Code : 'PKG_TPCDS_TRANSACTIONS'

Invalid Objects (0)
Invalid Tables : 
Invalid Indexes : 
Invalid Views : 
Invalid Sequences : 
Invalid Code : 

Missing Objects (22)
Missing Tables : 
Missing Indexes : 'CALL_CENTER_PK_IDX','CATALOG_PAGE_PK_IDX','CATALOG_RETURNS_PK_IDX','CATALOG_SALES_PK_IDX','CUSTOMER_PK_IDX','CA_ADDRESS_PK_IDX','CD_DEMO_PK_IDX','D_DATE_PK_IDX','HOUSEHOLD_DEMOGRAPHICS_PK_IDX','INCOME_BAND_PK_IDX','ITEM_PK_IDX','PROMOTION_PK_IDX','REASON_PK_IDX','SHIP_MODE_PK_IDX','STORE_PK_IDX','STORE_RETURNS_PK_IDX','STORE_SALES_PK_IDX','TIME_DIM_PK_IDX','W_WAREHOUSE_PK_IDX','WEB_PAGE_PK_IDX','WEB_RETURNS_PK_IDX','WEB_SALES_PK_IDX'
Missing Views : 
Missing Sequences : 
Missing Code : 
Schema Created

missing index 수동조치 완료

Data Generation Runtime Metrics
+-----------------------+-------------+
| Description           | Value       |
+-----------------------+-------------+
| Connection Time       | 0:00:00.000 |
| Data Generation Time  | 0:03:13.921 |
| DDL Creation Time     | 0:00:33.505 |
| Total Run Time        | 0:03:49.858 |
| Rows Inserted per sec | 5,457       |
+-----------------------+-------------+

Validation Report                                                                       
The schema appears to have been created successfully.

Valid Objects
Valid Tables : 'PASSENGERCOLLECTION','PASSENGER_METADATA'
Valid Indexes : 'PASSENGERCOLLECTION_PK','FIRST_NAME_INDEX','LAST_NAME_INDEX'
Valid Views : 
Valid Sequences : 'PASSENGER_SEQ'
Valid Code : 
Schema Created
```

---
#swingbench