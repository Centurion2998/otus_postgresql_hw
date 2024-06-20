Домашнее задание : секционаравние 

- Для секционирования создал новую таблицу. Добавил три секции, 2 поменьше для примера, третью диапазон побольше чтобы мог добавить данные

```
CREATE TABLE bookings.bookings_parttion (
	book_ref bpchar(6) NOT NULL,
	book_date timestamptz NOT NULL,
	total_amount numeric(10, 2) NOT NULL
)
partition by range(book_date);

CREATE TABLE bookings_parttion_1 PARTITION OF bookings_parttion 
       FOR VALUES FROM ('2017-06-01'::timestamptz) TO ('2017-07-01'::timestamptz);
CREATE TABLE bookings_parttion_2 PARTITION OF bookings_parttion 
       FOR VALUES FROM ('2017-07-01'::timestamptz) TO ('2017-08-01'::timestamptz);
CREATE TABLE bookings_parttion_3 PARTITION OF bookings_parttion 
       FOR VALUES FROM ('2010-07-01'::timestamptz) TO ('2015-08-01'::timestamptz);
```

-- Посмотрим сколько данных в секциях

```
SELECT tableoid::regclass, count(*) FROM bookings_parttion GROUP BY tableoid;

tableoid	            count
bookings_parttion_1	  7303
bookings_parttion_2	  5
```


- Пример в двух запросах.  Первый по секционируемой таблице, поиск будет быстрее, т.к сканирует сразу секцию в которой лежит дата.
Второй запрос выполняется дольше т.к он сканирует всю таблицу полностью.

```

explain (analyze)
select *
from bookings.bookings_parttion 
where book_date = '2017-06-25 21:15:00.000 +0300'

Seq Scan on bookings_parttion_1 bookings_parttion  (cost=0.00..138.29 rows=2 width=21) (actual time=0.015..1.246 rows=1 loops=1)
  Filter: (book_date = '2017-06-25 21:15:00+03'::timestamp with time zone)
  Rows Removed by Filter: 7302
Planning Time: 0.118 ms
Execution Time: 1.266 ms


explain (analyze)
select *
from bookings.bookings
where book_date = '2017-06-25 21:15:00.000 +0300'

Gather  (cost=1000.00..4606.76 rows=5 width=21) (actual time=0.338..88.635 rows=1 loops=1)
  Workers Planned: 1
  Workers Launched: 1
  ->  Parallel Seq Scan on bookings  (cost=0.00..3606.26 rows=3 width=21) (actual time=22.950..61.674 rows=0 loops=2)
        Filter: (book_date = '2017-06-25 21:15:00+03'::timestamp with time zone)
        Rows Removed by Filter: 131394
Planning Time: 0.080 ms
Execution Time: 88.659 ms

```
