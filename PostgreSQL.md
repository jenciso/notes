## Create database
```
[root@host]# su - postgres
Password:
bash-4.1$ createdb testdb
bash-4.1$ psql testdb
psql (8.4.13, server 9.2.4)

test=#
```
## Login another host
```
psql -h myhost -d mydb -U myuser
```

## Login another host using password
```
psql -h myhost -d mydb -U myuser -W
```

## List all databases
```
-bash-4.2$ /usr/pgsql-9.5/bin/psql
psql (9.5.3)
Type "help" for help.
postgres=# \l

```
## List all databases with disk usage
```
postgres=# \l+
```
## Switch to database
```
postgres=# \c database_name
```

## List all tables in the current database
```
postgres=# \dt *.


## Import a dump file
```
psql databasename < data_base_dump
```


