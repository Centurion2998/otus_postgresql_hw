- Домашнее задание : Бэкапы

- Создал ВМ с ПГ
- Создаем БД, схему и в ней таблицу. Заполним таблицы автосгенерированными 100 записями.
  
```
postgres=# create database homework9;

CREATE DATABASE

postgres=# \c homework9;

psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))

Вы подключены к базе данных "homework9" как пользователь "postgres".

homework9=# create schema worksch;

CREATE SCHEMA

homework9=# create table worksch.backup_table as select generate_series(1, 100) as id, md5(random()::text)::char(10) as value;

SELECT 100

```

- Сохраним бекап. Создадим вторую таблицу в которую загрузим этот бекап
  
```
postgres=# \c homework9

homework9=# create table worksch.backup_table_2 (id int, value char(10));
CREATE TABLE

\copy students to '/tmp/backup_copy.sql';
COPY 100
\copy students from '/tmp/backup_copy.sql';
COPY 100

homework9=# select * from worksch.backup_table_2 limit 10;

 id |   value    

----+------------

  1 | e873df7a83

  2 | 90ad395ba1

  3 | 17508b3f57

  4 | 37cabdf4ae

  5 | 7e04324e7a

  6 | 943a4b568a

  7 | 076751e68e

  8 | 42e78258e5

  9 | d762105370

 10 | 9691142818

(10 строк)

```

- C помощью утилиты pg_dump создадим бекап, а с помощью pg_restore восстановим в новую бд

```
pg_dump -d homework9 -t worksch.backup_table -t worksch.backup_table_2 -Fc | gzip > tables_copy.gz

gzip -d tables_copy.gz | pg_restore -d homework9_2 -n worksch -t backup_table_2 tables_copy
```
