- Домашнее задание : Работа с join`ами

-  inner join
  
```
-- inner join позволяет соеденять таблицы по одному или нескольким полям, и будут выводиться только те связки, у которых есть
точное совпадение. В нашем случае соеденили места и билеты, чтобы узнать какие места относятся к комфорту

select seat_no
from boarding_passes t1 
inner join ticket_flights t2 on t1.ticket_no = t2.ticket_no 
where t2.fare_conditions = 'Comfort'
```

- left join (right join)

```

-- В данном случае соеденили таблицу билетов и посадочные места, таким обарзом чтобы выводились только те которые относятся к комфорту.
Если условие в соедении выполняется, то будет в выборке будет выводиться значения всех столбцов таблиц, если же фильтр не работает,
то будет выводиться только поля таблицы из основной таблицы, а поля соединяемой, будут null

select *
from ticket_flights t1
left join boarding_passes t2 on t1.ticket_no = t2.ticket_no and t1.fare_conditions = 'Comfort'

```

- cross join
```
-- cross join перемножает каждую строчку стобца, на все строчки другой. Без фильра лучше не использовать :с

select t2.ticket_no,t2.seat_no
from ticket_flights t1
cross join boarding_passes t2

```

- full join

```
-- пример для такого соединения тяжело найти. В моем случе это вышло тоже самое что и left join .
Но принцип такой, что работает как одновременно работающие left и right join.
При соединении, если есть в основной таблице но нет в соединяемой, во второй будут null значения и наоборот

select t1.ticket_no ,t2.ticket_no 
from ticket_flights t1
full join boarding_passes t2 on t1.ticket_no = t2.ticket_no and t2.ticket_no = '0005432159776'

```

- Несколько разных соединений 
  
```
-- для каждого аэропорта получим все посадочные талоны рейсов за 12 июня по МСК

select * from airports_data ad 
	left join flights f on ad.airport_code = f.departure_airport
	join ticket_flights tf on tf.flight_id = f.flight_id 
	join boarding_passes bp on bp.ticket_no = tf.ticket_no and bp.flight_id = f.flight_id 
where  '2017-06-12 00:00:00.000 +0300' < f.actual_departure and f.actual_departure < '2017-06-12 23:59:59.999 +0300'
```
