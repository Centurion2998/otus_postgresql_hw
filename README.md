- Домашнее задание : Механизм блокировок

- Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд. Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.
  
```
ALTER SYSTEM SET log_lock_waits = on;

ALTER SYSTEM SET deadlock_timeout = '200';

SELECT pg_reload_conf();

SHOW deadlock_timeout;
```

- Смоделируйте ситуацию обновления одной и той же строки тремя командами UPDATE в разных сеансах. Изучите возникшие блокировки в представлении pg_locks и убедитесь, что все они понятны. Пришлите список блокировок и объясните, что значит каждая.

```
create database homework7;

CREATE TABLE accounts( id serial4, amount numeric );

INSERT INTO accounts VALUES (1,1000.00), (2,2000.00), (3,3000.00);
```

- В первой сессии запустил
  
```
BEGIN;
UPDATE accounts SET amount = amount - 100.00 WHERE id = 1;
```

- Во второй

```
BEGIN;
UPDATE accounts SET amount = amount + 100.00 WHERE id = 1;
```

- В pg_stat_activity нашел pid второй сессии для анализа
  
```
SELECT locktype, relation::REGCLASS, virtualxid AS virtxid, transactionid AS xid, mode, granted
FROM pg_locks 
where pid = 9380
 
```

- Проанализируем полученые блокировки
  
```
ExclusiveLock - монопольная блокировка, то есть доступ к объекту имеет только одна транзакция/сеанс. Другие транзакции не могут получить никакой разделяемой блокировки. Монопольная блокировка не совместима с любыми типами блокировок. 

ShareLock - разделяемая блокировка. Вторая транзакция пытается получить разделяемую блокировку для нужной записи. Доступ предоставлен не был, так как на запись наложена монопольная блокировка (ExclusiveLock) первой транзакции. Вторая транзакция ждёт завершения первой.

RowExclusiveLock - блокировка в этом режиме получает любая команда, которая изменяет данные в таблице.
```

- Воспроизведите взаимоблокировку трех транзакций. Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?

- Да, можно. /var/log/postgresql/postgresql-15-main.log:
  
```
ERROR:  deadlock detected
DETAIL:  
Process 15003 waits for ShareLock on transaction 2611256; blocked by process 3033.
Process 3033 waits for ShareLock on transaction 2611257; blocked by process 14567.
Process 14567 waits for ShareLock on transaction 2611258; blocked by process 15003.
Process 15003: update accounts set amount = 7000 where id = 2;
Process 3033: update accounts set amount = 9000 where id = 3;
Process 14567: update accounts set amount = 1000 where id = 1;
```
- Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?
  
- Да, могут. При обновлении первого запроса данных так как есть , а второй запрос обовляет данные получает их из индекса


