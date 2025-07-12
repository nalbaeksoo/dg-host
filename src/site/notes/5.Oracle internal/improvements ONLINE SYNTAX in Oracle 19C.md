---
{"dg-publish":true,"permalink":"/5.Oracle internal/improvements ONLINE SYNTAX in Oracle 19C/","dgPassFrontmatter":true,"noteIcon":""}
---


2023-05-19 / 16:52 


Tell me about the improvements to the ONLINE SYNTAX in Oracle 19C over previous versions and give me as much complete SQL as possible. older version (11.2/12.1/12.2)

Oracle 19c is part of Oracle's long-term support release and offers various enhancements and new features compared to previous versions. While there are no specific improvements in the SQL syntax itself, Oracle 19c introduces some new features and improvements that can be helpful while working with SQL.

1.  Real-Time Statistics: This feature allows the optimizer to make more informed decisions by providing accurate and up-to-date statistics. In older versions, the statistics would become stale over time, leading to suboptimal execution plans.
    
2.  Automatic Indexing: Oracle 19c introduces Automatic Indexing, a self-tuning feature that creates, rebuilds, and drops indexes based on the changing workload patterns. This feature can lead to better query performance without manual intervention.
    
3.  SQL Quarantine: In Oracle 19c, SQL Quarantine is introduced to improve system stability. It detects and prevents the execution of resource-intensive SQL statements that could cause performance degradation. In older versions, you would have to manually identify and tune such problematic queries.
    
4.  Active Data Guard DML Redirection: Oracle 19c introduces DML Redirection for Active Data Guard, allowing read-write workloads on the standby database. This feature can be helpful to offload some of the DML operations from the primary database to the standby database, thereby improving performance.
    
5.  High-Frequency Automatic Optimizer Statistics Collection: Oracle 19c introduces high-frequency automatic optimizer statistics collection, which collects statistics more frequently, leading to better query performance.
    

Note that these are just a few of the many enhancements and new features in Oracle 19c, but these improvements don't directly change the SQL syntax.

Here is an example SQL query that would work in both older versions and Oracle 19c:

sqlCopy code

`-- Create a sample table CREATE TABLE employees (   employee_id NUMBER PRIMARY KEY,   first_name VARCHAR2(50),   last_name VARCHAR2(50),   hire_date DATE,   salary NUMBER );  -- Insert data into the table INSERT INTO employees VALUES (1, 'John', 'Doe', TO_DATE('2020-01-01', 'YYYY-MM-DD'), 50000); INSERT INTO employees VALUES (2, 'Jane', 'Smith', TO_DATE('2020-05-15', 'YYYY-MM-DD'), 55000); INSERT INTO employees VALUES (3, 'Mike', 'Johnson', TO_DATE('2020-08-01', 'YYYY-MM-DD'), 52000);  -- Commit the data COMMIT;  -- Query the data SELECT employee_id, first_name, last_name, hire_date, salary FROM employees WHERE salary > 50000 ORDER BY hire_date ASC;`

This SQL script creates a table, inserts data, and then queries the data. It uses standard SQL syntax and would work in both older versions and Oracle 19c.

Tell me about the online sql syntax except for component. For example, partition online merge.

Online operations in Oracle allow you to perform maintenance tasks while the database remains available for users. This minimizes the downtime associated with such tasks, which can be crucial for maintaining high availability in production environments. Online SQL operations include partitioning, index creation or rebuilding, and data file movement.

1.  Online Partition Merge:

In Oracle, you can merge partitions online without affecting the availability of the partitioned table. This operation combines two adjacent partitions or subpartitions into a single partition while the table remains accessible for DML operations. Here's an example of an online partition merge:

sqlCopy code

`-- Create a partitioned table CREATE TABLE sales (   sales_id NUMBER,   sales_date DATE,   amount NUMBER ) PARTITION BY RANGE (sales_date)   (PARTITION sales_q1 VALUES LESS THAN (TO_DATE('2023-04-01', 'YYYY-MM-DD')),    PARTITION sales_q2 VALUES LESS THAN (TO_DATE('2023-07-01', 'YYYY-MM-DD')),    PARTITION sales_q3 VALUES LESS THAN (TO_DATE('2023-10-01', 'YYYY-MM-DD')),    PARTITION sales_q4 VALUES LESS THAN (TO_DATE('2024-01-01', 'YYYY-MM-DD')));  -- Merge partitions sales_q1 and sales_q2 online ALTER TABLE sales MERGE PARTITIONS sales_q1, sales_q2   INTO PARTITION sales_h1 ONLINE;`

In this example, we create a partitioned table and then merge two partitions, `sales_q1` and `sales_q2`, into a new partition called `sales_h1`. The `ONLINE` keyword ensures that this operation is performed online, so the table remains available for DML operations.

2.  Online Index Creation or Rebuilding:

You can create or rebuild an index online, allowing DML operations to be performed concurrently on the underlying table. Here's an example of online index creation and rebuilding:

sqlCopy code

`-- Create an index online CREATE INDEX idx_sales_amount ON sales(amount) ONLINE;  -- Rebuild an index online ALTER INDEX idx_sales_amount REBUILD ONLINE;`

In this example, we create and rebuild an index online using the `ONLINE` keyword. This ensures that the table remains available for DML operations during the index creation or rebuilding process.

3.  Online Data File Movement:

Oracle allows you to move data files online while the associated tablespaces remain available for DML operations. Here's an example of online data file movement:

sqlCopy code

`-- Move a data file online ALTER DATABASE MOVE DATAFILE '/u01/oradata/ORCL/users01.dbf'   TO '/u02/oradata/ORCL/users01.dbf' ONLINE;`

In this example, we move a data file from one location to another using the `ALTER DATABASE MOVE DATAFILE` command with the `ONLINE` keyword. This ensures that the associated tablespaces remain available for DML operations during the data file movement process.

Give me another case. Be detailed.

Here's another case that demonstrates the online splitting of a partition while the table remains accessible for DML operations. The example includes the creation of a partitioned table, adding data to the table, and then splitting one of the partitions online.

1.  Create a partitioned table:

sqlCopy code

`-- Create a partitioned table CREATE TABLE orders (   order_id NUMBER,   order_date DATE,   customer_id NUMBER,   amount NUMBER ) PARTITION BY RANGE (order_date)   (PARTITION orders_q1 VALUES LESS THAN (TO_DATE('2023-04-01', 'YYYY-MM-DD')),    PARTITION orders_q2 VALUES LESS THAN (TO_DATE('2023-07-01', 'YYYY-MM-DD')),    PARTITION orders_q3 VALUES LESS THAN (TO_DATE('2023-10-01', 'YYYY-MM-DD')),    PARTITION orders_q4 VALUES LESS THAN (TO_DATE('2024-01-01', 'YYYY-MM-DD')));`

In this example, we create a partitioned table called `orders` with four partitions based on the `order_date` column.

2.  Add data to the table:

sqlCopy code

`-- Insert data into the table INSERT INTO orders VALUES (1, TO_DATE('2023-01-15', 'YYYY-MM-DD'), 101, 1500); INSERT INTO orders VALUES (2, TO_DATE('2023-02-18', 'YYYY-MM-DD'), 102, 2000); INSERT INTO orders VALUES (3, TO_DATE('2023-03-21', 'YYYY-MM-DD'), 103, 2500); INSERT INTO orders VALUES (4, TO_DATE('2023-05-12', 'YYYY-MM-DD'), 104, 1800); INSERT INTO orders VALUES (5, TO_DATE('2023-06-20', 'YYYY-MM-DD'), 105, 2200);  -- Commit the data COMMIT;`

We insert some data into the `orders` table and commit the transaction.

3.  Split one of the partitions online:

sqlCopy code

`-- Split partition orders_q2 online ALTER TABLE orders SPLIT PARTITION orders_q2   AT (TO_DATE('2023-06-01', 'YYYY-MM-DD'))   INTO (PARTITION orders_q2a, PARTITION orders_q2b) ONLINE;`

In this example, we split the partition `orders_q2` into two partitions, `orders_q2a` and `orders_q2b`, at a specific date using the `SPLIT PARTITION` command with the `ONLINE` keyword. This ensures that the table remains available for DML operations during the partition splitting process.

Now the `orders` table has five partitions: `orders_q1`, `orders_q2a`, `orders_q2b`, `orders_q3`, and `orders_q4`. The data in the original `orders_q2` partition is distributed across the new `orders_q2a` and `orders_q2b` partitions based on the specified split date.

This example demonstrates the use of online partition splitting in Oracle, which helps maintain high availability by allowing DML operations to continue on the table while the partition is being split.

Aside from partition, what else can it do?

Apart from partitioning, Oracle supports several other online SQL operations that improve availability and performance. Some of these operations include:

1.  Online Table Redefinition:

Oracle allows you to redefine a table's structure online using the DBMS_REDEFINITION package. This enables you to make changes such as adding, dropping, or modifying columns, as well as changing the table's organization, storage, or partitioning scheme without affecting the table's availability. Here's an example of online table redefinition to add a new column:

sqlCopy code

`-- Create an interim table with the desired structure CREATE TABLE employees_interim AS SELECT * FROM employees; ALTER TABLE employees_interim ADD email VARCHAR2(255);  -- Begin redefinition BEGIN   DBMS_REDEFINITION.start_redef_table(     uname => 'SCHEMA_NAME',     orig_table => 'EMPLOYEES',     int_table => 'EMPLOYEES_INTERIM'   ); END; /  -- Copy dependent objects (indexes, triggers, etc.) DECLARE   l_num_errors PLS_INTEGER; BEGIN   DBMS_REDEFINITION.copy_table_dependents(     uname => 'SCHEMA_NAME',     orig_table => 'EMPLOYEES',     int_table => 'EMPLOYEES_INTERIM',     num_errors => l_num_errors   ); END; /  -- Finish redefinition BEGIN   DBMS_REDEFINITION.finish_redef_table(     uname => 'SCHEMA_NAME',     orig_table => 'EMPLOYEES',     int_table => 'EMPLOYEES_INTERIM'   ); END; /  -- Drop the interim table DROP TABLE employees_interim;`

This example demonstrates how to use the DBMS_REDEFINITION package to add a new column to the `employees` table online. The table remains available for DML operations during the redefinition process.

2.  Online Index Creation or Rebuilding:

As mentioned earlier, you can create or rebuild an index online, allowing DML operations to be performed concurrently on the underlying table. Here's an example of online index creation and rebuilding:

sqlCopy code

`-- Create an index online CREATE INDEX idx_orders_customer_id ON orders(customer_id) ONLINE;  -- Rebuild an index online ALTER INDEX idx_orders_customer_id REBUILD ONLINE;`

3.  Online Data File Movement:

Oracle allows you to move data files online while the associated tablespaces remain available for DML operations. Here's an example of online data file movement:

sqlCopy code

`-- Move a data file online ALTER DATABASE MOVE DATAFILE '/u01/oradata/ORCL/users01.dbf'   TO '/u02/oradata/ORCL/users01.dbf' ONLINE;`

4.  Online Materialized View Refresh:

Oracle allows you to refresh materialized views online, enabling you to update the materialized view with the latest data from the base tables while the materialized view remains available for querying. Here's an example of online materialized view refresh:

sqlCopy code

`-- Create a materialized view CREATE MATERIALIZED VIEW order_summary   BUILD IMMEDIATE   REFRESH FAST ON DEMAND   AS SELECT customer_id, COUNT(order_id) AS num_orders, SUM(amount) AS total_amount      FROM orders      GROUP BY customer_id;  -- Refresh the materialized view online DBMS_MVIEW.REFRESH('ORDER_SUMMARY', 'C', atomic_refresh => FALSE);`

In this example, we create a materialized view called `order_summary` and refresh it online using the DBMS_MVIEW.REFRESH procedure with the `atomic_refresh` parameter set to `FALSE`.

These are just a few examples of online SQL operations in Oracle that help maintain high availability and improve performance.

What else is there to know?

Oracle Database is a powerful and feature-rich RDBMS that offers a variety of capabilities to manage and process data efficiently. Apart from the online SQL operations we discussed earlier, here are some additional aspects and features of Oracle Database that you might find useful:

1.  Advanced Compression: Oracle Database provides advanced compression techniques for reducing storage space requirements and improving performance. This includes table compression, index compression, and LOB (Large Object) compression.
    
2.  In-Memory Database: Oracle Database In-Memory enables real-time analytics by accelerating performance for both analytic and mixed workloads. It uses a dual-format architecture that retains data in both the row and columnar format, providing faster access to data for analytical queries.
    
3.  Partitioning: Oracle Database provides advanced partitioning methods, such as range, list, hash, and composite partitioning, allowing you to distribute large tables across multiple partitions for better performance and manageability.
    
4.  Security Features: Oracle Database offers a wide range of security features, including data encryption, network encryption, data masking, database vault, and audit vault, to protect sensitive data and comply with regulatory requirements.
    
5.  Backup and Recovery: Oracle Database provides a comprehensive backup and recovery solution, including RMAN (Recovery Manager), Flashback technology, and Data Pump, to ensure data protection and fast recovery from data loss or corruption.
    
6.  Data Guard: Oracle Data Guard provides high availability, data protection, and disaster recovery for Oracle databases. It maintains one or more synchronized copies (standby databases) of the primary database to protect against data loss and minimize downtime during planned or unplanned outages.
    
7.  Real Application Clusters (RAC): Oracle RAC enables multiple instances of Oracle Database to run on different servers while accessing the same database, providing high availability, scalability, and load balancing.
    
8.  Performance Tuning: Oracle Database offers a variety of performance tuning features, including the optimizer, execution plans, SQL hints, and various dynamic performance views (V$ views) that help identify and resolve performance issues.
    
9.  PL/SQL: PL/SQL is Oracle's procedural language extension to SQL. It allows you to write procedural code, such as loops, conditional statements, and exception handling, and create stored procedures, functions, triggers, and packages for better modularity and reusability.
    
10.  Application Development Frameworks: Oracle offers various frameworks, such as Oracle Application Express (APEX), Oracle Forms, and Oracle JDeveloper, to develop and deploy web applications, forms, and reports that interact with Oracle Database.
    

These are just a few of the many features and capabilities of Oracle Database. By understanding and utilizing these features effectively, you can build and manage robust, scalable, and high-performing database systems.

#online #command #operation #syntax