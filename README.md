- Домашнее задание : Триггеры, поддержка заполнения витрин

- Создание тестовых таблиц и заполнение их данными

```
-- товары:
drop table if exists goods
CREATE TABLE goods
(
    goods_id    integer PRIMARY KEY,
    good_name   varchar(63) NOT NULL,
    good_price  numeric(12, 2) NOT NULL CHECK (good_price > 0.0)
);
INSERT INTO goods (goods_id, good_name, good_price)
VALUES 	(1, 'Спички хозайственные', .50),
		(2, 'Автомобиль Ferrari FXX K', 185000000.01);

-- Продажи
	
drop table if exists 	sales
CREATE TABLE sales(
    sales_id    integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    goods_id     integer REFERENCES goods (goods_id),
    sales_time  timestamp with time zone DEFAULT now(),
    sales_qty   integer CHECK (sales_qty > 0));

INSERT INTO sales (goods_id, sales_qty) VALUES (1, 10), (1, 1), (1, 120), (2, 1);

drop table if exists good_sum_mart
CREATE TABLE good_sum_mart
(
	good_name   varchar(63) NOT NULL,
	sum_sale	numeric(16, 2)NOT NULL
);
```

- Создание функции для тригера и самого тригера

```
CREATE OR REPLACE FUNCTION tg_sales()
RETURNS trigger
AS
$$
DECLARE
    v_summa  numeric(16, 2);
    v_name   text;

BEGIN
    
CASE TG_OP 
WHEN 'INSERT' then
 
INSERT INTO good_sum_mart (good_name, sum_sale) 
SELECT G.good_name, (G.good_price * NEW.sales_qty) as sum_sale
FROM goods G
WHERE G.goods_id = NEW.goods_id;

RETURN NEW;

WHEN 'UPDATE' THEN

SELECT G.good_name, (G.good_price * NEW.sales_qty) as sum_sale
INTO v_name,v_summa
FROM goods G
WHERE G.goods_id = NEW.goods_id;

UPDATE good_sum_mart
SET sum_sale = v_summa
WHERE good_name = v_name;

RETURN NEW;

WHEN 'DELETE' THEN
       
SELECT G.good_name
INTO v_name
FROM goods G
WHERE G.goods_id = OLD.goods_id;   

DELETE FROM good_sum_mart
where good_name = v_name;

END CASE;

END;
$$ 
LANGUAGE plpgsql
 

CREATE TRIGGER tg_sales
AFTER INSERT OR UPDATE OR DELETE
ON sales
FOR ROW
EXECUTE FUNCTION tg_sales();

```

- Посмотри на заполненные данные в таблицах

```
select *
from goods

goods_id  |   good_name	               		| good_price
-------------------------------------------------------------------
1	  |  Спички хозайственные	        |  0,5
-------------------------------------------------------------------
2	  |      Автомобиль Ferrari FXX K	| 185 000 000,01

select *
from sales

sales_id   |	goods_id    |	 sales_time	                |    sales_qty
-------------------------------------------------------------------------------
25	   |     1	    |   2024-06-20 22:12:04.063 +0300	|    10
-------------------------------------------------------------------------------
26	   |     1	    |   2024-06-20 22:12:04.063 +0300	|    1
-------------------------------------------------------------------------------
27	   |     1	    |  2024-06-20 22:12:04.063 +0300	|    120
-------------------------------------------------------------------------------
28	   |     2          |   2024-06-20 22:12:04.063 +0300	|     1

select *
from good_sum_mart

good_name	           |       sum_sale
----------------------------------------------------
Спички хозайственные	   |         5
----------------------------------------------------
Спички хозайственные	   |         0,5
----------------------------------------------------
Спички хозайственные	   |         60
-----------------------------------------------------
Автомобиль Ferrari FXX K   |	  185 000 000,01


SELECT G.good_name, sum(G.good_price * S.sales_qty)
FROM goods G
INNER JOIN sales S ON S.goods_id = G.goods_id
GROUP BY G.good_name;

good_name	              |    sum
-------------------------------------------------------
Автомобиль Ferrari FXX K      |   185 000 000,01
-------------------------------------------------------
Спички хозайственные	      |   65,5

```

- Потестируем

```
INSERT INTO sales (goods_id, sales_qty) VALUES (1, 10);

good_name                   |	 sum
--------------------------------------------------------
Автомобиль Ferrari FXX K    |	  185 000 000,01
--------------------------------------------------------
Спички хозайственные	     |     70,5


update sales set sales_qty = 3 where sales_id = 25;

good_name	             |     sum
-------------------------------------------------------
Автомобиль Ferrari FXX K     |     185 000 000,01
-------------------------------------------------------
Спички хозайственные	     |      67
```

- Доработка. Изменил код тригера , и добавил уникальность для стобца good_name в таблице good_sum_mart  

```
alter table good_sum_mart add unique(good_name);


CREATE OR REPLACE FUNCTION tg_sales()
RETURNS trigger
AS
$$
DECLARE
    v_summa  numeric(16, 2);
    v_summa_del numeric(16, 2);
    v_name   text;

BEGIN
    
CASE TG_OP 
WHEN 'INSERT' then

SELECT G.good_name, (G.good_price * NEW.sales_qty) as sum_sale
into v_name,v_summa
FROM goods G
WHERE G.goods_id = NEW.goods_id;

if not exists (select 1 from good_sum_mart GM inner join goods G on G.good_name= GM.good_name where G.goods_id = new.goods_id)
	then 
		insert into good_sum_mart(good_name,sum_sale) 
		select good_name,0 
	    from goods 
	    where goods_id = new.goods_id;
	end if;

update  good_sum_mart
set sum_sale = sum_sale + v_summa
where good_name = v_name;

RETURN NEW;

WHEN 'UPDATE' THEN

SELECT G.good_name, (G.good_price * NEW.sales_qty), (G.good_price * OLD.sales_qty)
INTO v_name,v_summa,v_summa_del
FROM goods G
WHERE G.goods_id = OLD.goods_id;

UPDATE good_sum_mart
SET sum_sale = sum_sale + v_summa - v_summa_del
WHERE good_name = v_name;

RETURN NEW;

WHEN 'DELETE' THEN
       
SELECT G.good_name, (G.good_price * OLD.sales_qty)
INTO v_name , v_summa_del
FROM goods G
WHERE G.goods_id = OLD.goods_id;   

update  good_sum_mart
set sum_sale = sum_sale - v_summa_del
where good_name = v_name;


END CASE;

END;
$$ 
LANGUAGE plpgsql
```



