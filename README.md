- Домашняя работа : Работа с индексами

- Создал индекс на тестовую таблицу
  
```
create index bookings_table_idx on bookings.bookings_table (book_ref)

explain(analyze)
select *
from bookings.bookings_table 
where book_ref = '00000F'

Index Scan using bookings_table_idx on bookings_table  (cost=0.42..8.44 rows=1 width=21) (actual time=0.120..0.122 rows=1 loops=1)
  Index Cond: (book_ref = '00000F'::bpchar)
Planning Time: 0.107 ms
Execution Time: 0.136 ms
```

- Из резульатата видно индекс используется в запросе при фильрации

- Для реализации полнотекстового поиска необходимы функции, позволяющие создать tsvector из документа и tsquery из запроса пользователя.

```

update tickets
set passenger_name_full_scan = to_tsvector(passenger_name);

create index passenger_name_full_scan_index on tickets using GIN(passenger_name_full_scan);

explain (analyze)
select *
from tickets
where passenger_name_full_scan @@ to_tsquery('valeriy');

Bitmap Heap Scan on tickets  (cost=45.15..9389.22 rows=3728 width=140) (actual time=1.276..4.656 rows=3681 loops=1)
  Recheck Cond: (passenger_name_full_scan @@ to_tsquery('valeriy'::text))
  Heap Blocks: exact=2925
  ->  Bitmap Index Scan on passenger_name_full_scan_index  (cost=0.00..44.21 rows=3728 width=0) (actual time=0.733..0.733 rows=3681 loops=1)
        Index Cond: (passenger_name_full_scan @@ to_tsquery('valeriy'::text))
Planning Time: 0.445 ms
Execution Time: 4.830 ms

```

- Индекс с фильтром
  
```
create index bookings_filter_idx on bookings(total_amount) where total_amount >= 200000;

explain (analyze)
select total_amount
from bookings
where total_amount >= 200000;

Bitmap Heap Scan on bookings  (cost=292.35..2186.95 rows=17648 width=6) (actual time=2.102..16.551 rows=17531 loops=1)
  Recheck Cond: (total_amount >= '200000'::numeric)
  Heap Blocks: exact=1674
  ->  Bitmap Index Scan on bookings_filter_idx  (cost=0.00..287.94 rows=17648 width=0) (actual time=1.834..1.834 rows=17531 loops=1)
Planning Time: 0.388 ms
Execution Time: 17.431 ms
```

- Индекс на несколько полей

```
create index seats_idx on seats(aircraft_code, seat_no); 


explain (analyze)
select * 
from seats f 
where aircraft_code = '319' and seat_no = '2A';

Index Scan using seats_idx on seats f  (cost=0.28..8.30 rows=1 width=15) (actual time=0.113..0.114 rows=1 loops=1)
  Index Cond: ((aircraft_code = '319'::bpchar) AND ((seat_no)::text = '2A'::text))
Planning Time: 0.142 ms
Execution Time: 0.129 ms
``
