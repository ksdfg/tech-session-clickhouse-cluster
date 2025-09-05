# 2s2r

## Streaming logs

```bash
# logs-s1-r1
sudo echo
(sudo tail -f logs-s1-r1/ch.log | grep --line-buffered test_db | jq) &

# logs-s1-r2
sudo echo
(sudo tail -f logs-s1-r2/ch.log | grep --line-buffered test_db | jq) &

# logs-s2-r1
sudo echo
(sudo tail -f logs-s2-r1/ch.log | grep --line-buffered test_db | jq) &

# logs-s2-r2
sudo echo
(sudo tail -f logs-s2-r2/ch.log | grep --line-buffered test_db | jq) &
```

## Create database and tables

s1-r1
```sql
CREATE DATABASE test_db ON CLUSTER mycluster;

CREATE TABLE test_db.test_table_local ON CLUSTER mycluster
(
    `id` UInt64,
    `message` String,
    `timestamp` DateTime
)
ENGINE = ReplicatedMergeTree
ORDER BY id;

SHOW CREATE TABLE test_db.test_table_local;

CREATE TABLE test_db.test_table ON CLUSTER mycluster  
ENGINE = Distributed(mycluster, test_db, test_table_local, rand());

SHOW CREATE TABLE test_db.test_table;
```

s2-r1
```sql
SHOW DATABASES;

SHOW TABLES FROM test_db;

SHOW CREATE TABLE test_db.test_table_local;

SHOW CREATE TABLE test_db.test_table;
```

## Insert data

s1-r1
```sql
INSERT INTO test_db.test_table VALUES  
(1, 'Message 1', now()),  
(2, 'Message 2', now()),  
(3, 'Message 3', now());

SELECT  
    hostName(),  
    count(*)  
FROM test_db.test_table  
GROUP BY hostName();

SELECT *  
FROM test_db.test_table;

SELECT *  
FROM test_db.test_table_local;
```

s2-r1
```sql
SELECT *  
FROM test_db.test_table;

SELECT  
hostName(),  
count(*)  
FROM test_db.test_table  
GROUP BY hostName();

SELECT *  
FROM test_db.test_table_local;
```
