- Домашнее задание : Настройка autovacuum с учетом особеностей производительности

- выполнить pgbench -i postgres
```
midly@midly-VirtualBox:/$ sudo -u postgres pgbench -i postgres

dropping old tables...

ЗАМЕЧАНИЕ:  таблица "pgbench_accounts" не существует, пропускается

ЗАМЕЧАНИЕ:  таблица "pgbench_branches" не существует, пропускается

ЗАМЕЧАНИЕ:  таблица "pgbench_history" не существует, пропускается

ЗАМЕЧАНИЕ:  таблица "pgbench_tellers" не существует, пропускается

creating tables...

generating data (client-side)...

100000 of 100000 tuples (100%) done (elapsed 0.30 s, remaining 0.00 s)

vacuuming...

creating primary keys...

done in 0.84 s (drop tables 0.00 s, create tables 0.04 s, client-side generate 0.49 s, vacuum 0.14 s, primary keys 0.17 s).
```

-- Запускаем pgbench -c8 -P 6 -T 60 -U postgres postgres

```
midly@midly-VirtualBox:/$ sudo -u postgres pgbench -c8 -P 6 -T 60 -U postgres postgres

pgbench (14.12 (Ubuntu 14.12-1.pgdg22.04+1))

starting vacuum...end.

progress: 6.0 s, 452.5 tps, lat 17.430 ms stddev 9.160

progress: 12.0 s, 489.3 tps, lat 16.336 ms stddev 6.514

progress: 18.0 s, 496.8 tps, lat 16.108 ms stddev 6.445

progress: 24.0 s, 495.5 tps, lat 16.140 ms stddev 5.875

progress: 30.0 s, 486.5 tps, lat 16.440 ms stddev 6.773

progress: 36.0 s, 484.8 tps, lat 16.491 ms stddev 6.619

progress: 42.0 s, 488.0 tps, lat 16.395 ms stddev 6.243

progress: 48.0 s, 483.3 tps, lat 16.551 ms stddev 6.144

progress: 54.0 s, 481.8 tps, lat 16.600 ms stddev 6.239

progress: 60.0 s, 485.3 tps, lat 16.478 ms stddev 7.797

transaction type: <builtin: TPC-B (sort of)>

scaling factor: 1

query mode: simple

number of clients: 8

number of threads: 1

duration: 60 s

number of transactions actually processed: 29072

latency average = 16.496 ms

latency stddev = 6.858 ms

initial connection time = 74.538 ms

tps = 484.432450 (without initial connection time)

```

- Поменял настройки и перезапустил PG  sudo nano /etc/postgresql/15/main/postgresql.conf
```
max_connections = 40
shared_buffers = 1GB
effective_cache_size = 3GB
maintenance_work_mem = 512MB
checkpoint_completion_target = 0.9
wal_buffers = 16MB
default_statistics_target = 500
random_page_cost = 4
effective_io_concurrency = 2
work_mem = 6553kB
min_wal_size = 4GB
max_wal_size = 16GB

sudo systemctl restart postgresql.
```

- Данные после повторного запуска . Мы увеличили доступные PG ресурсы . Производительность увеличилась
```
udo -u postgres pgbench -c8 -P 6 -T 60 -U postgres postgres

pgbench (14.12 (Ubuntu 14.12-1.pgdg22.04+1))

starting vacuum...end.

progress: 6.0 s, 524.2 tps, lat 15.063 ms stddev 5.368

progress: 12.0 s, 566.5 tps, lat 14.121 ms stddev 4.802

progress: 18.0 s, 449.8 tps, lat 17.754 ms stddev 8.125

progress: 24.0 s, 532.8 tps, lat 15.019 ms stddev 5.504

progress: 30.0 s, 565.3 tps, lat 14.147 ms stddev 4.810

progress: 36.0 s, 571.7 tps, lat 13.992 ms stddev 5.063

progress: 42.0 s, 577.5 tps, lat 13.851 ms stddev 4.463

progress: 48.0 s, 574.7 tps, lat 13.918 ms stddev 4.539

progress: 54.0 s, 581.0 tps, lat 13.766 ms stddev 4.315

progress: 60.0 s, 578.5 tps, lat 13.823 ms stddev 4.298

transaction type: <builtin: TPC-B (sort of)>

scaling factor: 1

query mode: simple

number of clients: 8

number of threads: 1

duration: 60 s

number of transactions actually processed: 33140

latency average = 14.470 ms

latency stddev = 5.285 ms

initial connection time = 67.730 ms

tps = 552.314177 (without initial connection time)

```

- Создал таблицу и заполнил данными. Далее обновил
```
idly@midly-VirtualBox:~$ sudo -u postgres psql -p 5432

could not change directory to "/home/midly": Отказано в доступе

psql (14.12 (Ubuntu 14.12-1.pgdg22.04+1))

Type "help" for help.



postgres=# CREATE TABLE book(id serial,name char(100));

CREATE TABLE

postgres=# INSERT INTO book(name) SELECT 'name' FROM generate_series(1,1000000);

INSERT 0 1000000

postgres=# select count(1) from book;

  count  

---------

 1000000

(1 row)
postgres=# SELECT pg_size_pretty(pg_total_relation_size('book'));

 pg_size_pretty 

----------------

 135 MB

(1 row)



postgres=# update book set name = 'kniga1';

UPDATE 1000000

postgres=# update book set name = 'kniga10';

UPDATE 1000000

postgres=# update book set name = 'kniga100';

UPDATE 1000000

postgres=# update book set name = 'kniga1000';

UPDATE 1000000

postgres=# update book set name = 'kniga10000';

UPDATE 1000000

```

- Посмотрел количество мертвых строчек в таблице и когда в последний раз был автовакум
```
postgres=# SELECT relname, n_live_tup, n_dead_tup,  

trunc(100*n_dead_tup/(n_live_tup+1))::float AS "ratio%", last_autovacuum  

FROM pg_stat_user_tables WHERE relname = 'book'; 

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        

---------+------------+------------+--------+-------------------------------

 book    |    1016987 |          0 |      0 | 2024-06-11 05:19:12.705279+03

(1 row)

```

- обнвовил 5 раз. Статистика
- 
```

postgres=# SELECT relname, n_live_tup, n_dead_tup,  

trunc(100*n_dead_tup/(n_live_tup+1))::float AS "ratio%", last_autovacuum  

FROM pg_stat_user_tables WHERE relname = 'book'; 

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        

---------+------------+------------+--------+-------------------------------

 book    |     999359 |          0 |      0 | 2024-06-11 05:25:18.865451+03

(1 row)





postgres=# SELECT pg_size_pretty(pg_total_relation_size('book'));

 pg_size_pretty 

----------------

 808 MB

(1 row)

```

- Отключил автовакум
```
postgres=# alter table book set (autovacuum_enabled = off);

ALTER TABLE

postgres=# SELECT relname, n_live_tup, n_dead_tup,

trunc(100*n_dead_tup/(n_live_tup+1))::float AS "ratio%", last_autovacuum

FROM pg_stat_user_tables WHERE relname = 'book';

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        

---------+------------+------------+--------+-------------------------------

 book    |     999359 |          0 |      0 | 2024-06-11 05:25:18.865451+03

(1 row)

```

- Обновил 10 раз строки
```
postgres=# SELECT relname, n_live_tup, n_dead_tup,

trunc(100*n_dead_tup/(n_live_tup+1))::float AS "ratio%", last_autovacuum

FROM pg_stat_user_tables WHERE relname = 'book';

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        

---------+------------+------------+--------+-------------------------------

 book    |     999359 |    9998813 |   1000 | 2024-06-11 05:25:18.865451+03

(1 row)



postgres=# SELECT pg_size_pretty(pg_total_relation_size('book'));

 pg_size_pretty 

----------------

 1482 MB

(1 row)
```

- Применил автовакум
```
postgres=# vacuum full;

VACUUM

postgres=# SELECT relname, n_live_tup, n_dead_tup,

trunc(100*n_dead_tup/(n_live_tup+1))::float AS "ratio%", last_autovacuum

FROM pg_stat_user_tables WHERE relname = 'book';

 relname | n_live_tup | n_dead_tup | ratio% |        last_autovacuum        

---------+------------+------------+--------+-------------------------------

 book    |     999359 |    9998813 |   1000 | 2024-06-11 05:25:18.865451+03

(1 row)



postgres=# SELECT pg_size_pretty(pg_total_relation_size('book'));

 pg_size_pretty 

----------------

 135 MB

(1 row)
``

- При обновлении старая строчка помечается на удаление и создается новая. Поэтому объем только увеличивался

- Задание со * . Написать функцию
```
do $$declare i integer;
begin
	FOR i IN 1..10
		LOOP
			update book set name = concat('name',i);
		END loop;
end$$;
```
