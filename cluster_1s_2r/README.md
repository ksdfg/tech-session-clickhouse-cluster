# 1s2r

## Streaming logs

```bash
# logs-ch1
sudo echo
(sudo tail -f logs-ch1/ch.log | grep --line-buffered test_db | jq) &

# logs-ch2
sudo echo
(sudo tail -f logs-ch2/ch.log | grep --line-buffered test_db | jq) &
```

## Create database and table

ch1
```sql
CREATE DATABASE test_db ON CLUSTER mycluster;

CREATE TABLE test_db.test_table ON CLUSTER mycluster
(
	`id` UInt64,
	`message` String,
	`timestamp` DateTime
)
ENGINE = ReplicatedMergeTree
ORDER BY id;

SHOW CREATE TABLE test_db.test_table;
```

ch2
```sql
SHOW DATABASES;

SHOW TABLES FROM test_db;

SHOW CREATE TABLE test_db.test_table;
```

## Insert data

ch1
```sql
INSERT INTO test_db.test_table VALUES  
(1, 'Message 1', now()),  
(2, 'Message 2', now()),  
(3, 'Message 3', now());

SELECT *  
FROM test_db.test_table;
```

ch2
```sql
SELECT *  
FROM test_db.test_table;
```
