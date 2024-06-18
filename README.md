# mariadb-replication-test

```
docker compose up
```

## On primary:

Create user the replica will use for replication:

```
create user 'slave1'@'%' identified by 'slave123';
grant replication slave on *.* to 'slave1'@'%';
```

`File` and `Position` values of the following statement are needed for `change master` statement on replica:

```
show master status;
```

## On replica:

```
stop slave;

change master to
    master_host = 'primary',
    master_user = 'slave1',
    master_password = 'slave123',
    master_log_file = 'primary-bin.000002',
    master_log_pos = 657;

start slave;

show slave status;
```

## Test replication

Execute on primary:

```
create table mariadb_replication_test.table1 (id int null);
insert into mariadb_replication_test.table1 (id) values (1);

create table mariadb_replication_test.table2 (id int null);
insert into mariadb_replication_test.table2 (id) values (2);
```

`table1` is replicated, `table2` is not (see replica config).
