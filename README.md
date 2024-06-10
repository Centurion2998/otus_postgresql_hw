- Домашнее задание : Работа с базами данных, пользователями и правами

- Создал новый кластер PG 14
  
```
midly@midly-VirtualBox:~$ pg_lsclusters

Ver Cluster Port Status Owner    Data directory              Log file

14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
```

- Создана база данных testdb

```
postgres=# CREATE DATABASE testdb;

CREATE DATABASE

postgres=# \l

                                  List of databases

   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   

-----------+----------+----------+-------------+-------------+-----------------------

 postgres  | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 | 

 template0 | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 | =c/postgres          +

           |          |          |             |             | postgres=CTc/postgres

 template1 | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 | =c/postgres          +

           |          |          |             |             | postgres=CTc/postgres

 testdb    | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 | 

(4 rows)

```

- Подключился к тестовой бд
```
postgres=# \c testdb

You are now connected to database "testdb" as user "postgres".

testdb=# select current_database();

 current_database 

------------------

 testdb

(1 row)

testdb=# create schema testnm;

CREATE SCHEMA

testdb=# CREATE TABLE t1 (i int);

CREATE TABLE

testdb=# insert into t1 values(1);

INSERT 0 1

testdb=# select * from t1;

 i 

---

 1

(1 row)


testdb=# create role readonly;

CREATE ROLE

testdb=# grant connect on database testdb to readonly;

GRANT

testdb=# grant usage on schema testnm to readonly;

GRANT

testdb=# grant SELECT on all TABLEs in SCHEMA testnm TO readonly;

GRANT

testdb=# CREATE USER testread with password 'test123';

CREATE ROLE

testdb=# grant readonly TO testread;

GRANT ROLE

testdb=# \c testdb testread

Вы подключены к базе данных "testdb" как пользователь "testread".

testdb=# select * from t1;

 i 

---

 1

(1 строка)

```

-  При использовании команды \c testdb testread возникала ошибка -- ВАЖНО:  пользователь "testread" не прошёл проверку подлинности (по паролю)
Изменил в файле pg_hba.conf на md5

```
testdb=# drop table t1;

DROP TABLE

testdb=# create table testnm.t1(c1 integer);
CREATE TABLE
testdb=# insert into testnm.t1 values(1);
INSERT 0 1
testdb=# \c testdb testread

Вы подключены к базе данных "testdb" как пользователь "testread".

Вы подключены к базе данных "testdb" как пользователь "testread".
testdb=> select * from testnm.t1;
 c1
----
  1
```

- Создание таблицы t2 прошло без проблем т.к у пользователя хватает прав на создание таблицы в схеме public
  
- После применения команд
```
REVOKE CREATE on SCHEMA public FROM public;
REVOKE ALL on DATABASE testdb FROM public;
```
- Создание таблицы t3 будет невозможно, т.к мы отобрали права на схему public
