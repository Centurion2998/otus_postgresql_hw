Домашнее задание : Работа с уровнями изоляции транзакции в PostgreSQL
1. Создана ВМ с ОС Ubuntu. Дополнительно установлена PostgreSQL. Для удобства поставил Ddeaver
2. В первой сессии создал таблицу и ее заполнил данными
  create table persons(id serial, first_name text, second_name text);
  insert into persons(first_name, second_name) values('ivan', 'ivanov');
  insert into persons(first_name, second_name) values('petr', 'petrov');
  commit;
show transaction isolation level -- уровень изоляции read committed
3. Добавлена строка  insert into persons(first_name, second_name) values('sergey', 'sergeev');
Новая строка во второй сессии не будет видна, т.к уровень изоляции read committed , а в первой сессии не был сделан commit
4. После заверешения транзакции командой commit , строка будет видна
5. В первой сессии добавил строку insert into persons(first_name, second_name) values('sveta', 'svetova') с уровнем изоляции
      set transaction isolation level repeatable read;
   Запись во второй сесси не будет видна, т.к в режиме Repeatable Read , видны только те данные, которые были зафиксированы до начала транзакции (Защита от фантомного чтения)
6. После выполнения commita в первой сессии , запись во второй, будет видна.
