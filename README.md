- Домашнее задание : Установка и настройка PostgreSQL

- Создана ВМ с ОС Ubuntu 22.04 LTS
- Установил PostgreSQL 15 + обновил
 
```
sudo apt update && sudo apt upgrade -y -q
```
- Проверил что кластер запущен
  
```
midly@midly-VirtualBox:~$ sudo -u postgres pg_lsclusters

Ver Cluster Port Status Owner    Data directory              Log file

15  main    5433 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

- Зашел из под пользователя postgres в psql и добавил произвольные строки
```
midly@midly-VirtualBox:~$ sudo -i -u postgres

postgres@midly-VirtualBox:~$ psql

psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))

Введите "help", чтобы получить справку.



postgres=# create table test(c1 text);

CREATE TABLE

postgres=# insert into test values('1');

INSERT 0 1

postgres=# 
```

- Остановил postgre
  
```
sudo -u postgres pg_ctlcluster 15 main stop

midly@midly-VirtualBox:~$ sudo -u postgres pg_lsclusters

Ver Cluster Port Status Owner    Data directory              Log file

15  main    5433 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
```

- Работая на ВМ установленной на своем ноутбуке, большая проблема с памятью ((
  виртуальные диски ( начинаются на /dev/loop) и всего оди честный диск /dev/sda на 40 гб
  Из-за малого кол-ва памяти, создал хард диск всего на 5гб

```
Диск /dev/sdb: 5 GiB, 5368709120 байт, 10485760 секторов

Disk model: VBOX HARDDISK   

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт
```

- Создание раздела на подключенном диске

```
midly@midly-VirtualBox:~$ sudo fdisk /dev/sdb



Добро пожаловать в fdisk (util-linux 2.37.2).

Изменения останутся только в памяти до тех пор, пока вы не решите записать их.

Будьте внимательны, используя команду write.



Устройство не содержит стандартной таблицы разделов.

Создана новая метка DOS с идентификатором 0xf148b457.



Команда (m для справки): m



Справка:



  DOS (MBR)

   a   переключение флага загрузки

   b   редактирование вложенной метки диска BSD

   c   переключение флага dos-совместимости



  Общие

   d   удалить раздел

   F   показать свободное неразмеченное пространство

   l   список известных типов разделов

   n   добавление нового раздела

   p   вывести таблицу разделов

   t   изменение типа раздела

   v   проверка таблицы разделов

   i   вывести информацию о разделе



  Разное

   m   вывод этого меню

   u   изменение единиц измерения экрана/содержимого

   x   дополнительная функциональность (только для экспертов)



  Сценарий

   I   загрузить разметку из файла сценария sfdisk

   O   записать разметку в файл сценария sfdisk



  Записать и выйти

   w   запись таблицы разделов на диск и выход

   q   выход без сохранения изменений



  Создать новую метку

   g   создание новой пустой таблицы разделов GPT

   G   создание новой пустой таблицы разделов SGI (IRIX)

   o   создание новой пустой таблицы разделов DOS

   s   создание новой пустой таблицы разделов Sun





Команда (m для справки): n

Тип раздела

   p   основной (0 primary, 0 extended, 4 free)

   e   расширенный (контейнер для логических разделов)

Выберите (по умолчанию - p): p

Номер раздела (1-4, по умолчанию 1): 

Первый сектор (2048-10485759, по умолчанию 2048): 

Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-10485759, по умолчанию 10485759): 



Создан новый раздел 1 с типом 'Linux' и размером 5 GiB.



Команда (m для справки): p

Диск /dev/sdb: 5 GiB, 5368709120 байт, 10485760 секторов

Disk model: VBOX HARDDISK   

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт

Тип метки диска: dos

Идентификатор диска: 0xf148b457



Устр-во    Загрузочный начало    Конец  Секторы Размер Идентификатор Тип

/dev/sdb1                2048 10485759 10483712     5G            83 Linux



Команда (m для справки): w

Таблица разделов была изменена.

Вызывается ioctl() для перечитывания таблицы разделов.

Синхронизируются диски.



midly@midly-VirtualBox:~$ sudo fdisk -l

Диск /dev/loop0: 38,83 MiB, 40714240 байт, 79520 секторов

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт





Диск /dev/loop1: 476 KiB, 487424 байт, 952 секторов

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт


Диск /dev/loop2: 6,41 MiB, 6725632 байт, 13136 секторов

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт


Диск /dev/sda: 40 GiB, 42949672960 байт, 83886080 секторов

Disk model: VBOX HARDDISK   

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт

Тип метки диска: gpt

Идентификатор диска: 3FE8DBFE-FBCC-4F4A-AB7F-F3814F047248



Устр-во     начало    Конец  Секторы Размер Тип

/dev/sda1     2048     4095     2048     1M BIOS boot

/dev/sda2     4096  1054719  1050624   513M EFI

/dev/sda3  1054720 83884031 82829312  39,5G Файловая система Linux




Диск /dev/sdb: 5 GiB, 5368709120 байт, 10485760 секторов

Disk model: VBOX HARDDISK   

Единицы: секторов по 1 * 512 = 512 байт

Размер сектора (логический/физический): 512 байт / 512 байт

Размер I/O (минимальный/оптимальный): 512 байт / 512 байт

Тип метки диска: dos

Идентификатор диска: 0xf148b457



Устр-во    Загрузочный начало    Конец  Секторы Размер Идентификатор Тип

/dev/sdb1                2048 10485759 10483712     5G            83 Linux



midly@midly-VirtualBox:~$ sudo lsblk

NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS

loop0    7:0    0  38,8M  1 loop /snap/snapd/21759

loop1    7:1    0   476K  1 loop /snap/snapd-desktop-integration/157

sda      8:0    0    40G  0 disk 

├─sda1   8:1    0     1M  0 part 

├─sda2   8:2    0   513M  0 part /boot/efi

└─sda3   8:3    0  39,5G  0 part /var/snap/firefox/common/host-hunspell

                                 /

sdb      8:16   0     5G  0 disk 

└─sdb1   8:17   0     5G  0 part 

sr0     11:0    1  1024M  0 rom  

midly@midly-VirtualBox:~$ sudo mkfs.ext4 /dev/sdb1

mke2fs 1.46.5 (30-Dec-2021)

Creating filesystem with 1310464 4k blocks and 327680 inodes

Filesystem UUID: 0e328047-430a-40cb-b765-40a73bc19fcc

Superblock backups stored on blocks: 

	32768, 98304, 163840, 229376, 294912, 819200, 884736



Allocating group tables: done                            

Сохранение таблицы inod'ов: done                            

Создание журнала (16384 блоков): готово

Writing superblocks and filesystem accounting information: готово

```


- Создал католог и смонтировал туда диск
  
```
midly@midly-VirtualBox:~$ sudo mkdir /mnt/sdb1

midly@midly-VirtualBox:~$ sudo mount /dev/sdb1 /mnt/sdb1

midly@midly-VirtualBox:~$ df -h

Файл.система   Размер Использовано  Дост Использовано% Cмонтировано в

tmpfs            485M         1,6M  484M            1% /run

/dev/sda3         39G          17G   21G           45% /

tmpfs            2,4G         1,1M  2,4G            1% /dev/shm

tmpfs            5,0M         4,0K  5,0M            1% /run/lock

/dev/sda2        512M         6,1M  506M            2% /boot/efi

tmpfs            485M         112K  485M            1% /run/user/1000

/dev/sdb1        4,9G          24K  4,6G            1% /mnt/sdb1
```

- Диск смонтирован

```
midly@midly-VirtualBox:~$ df -h

Файл.система   Размер Использовано  Дост Использовано% Cмонтировано в

tmpfs            485M         1,6M  484M            1% /run

/dev/sda3         39G          17G   21G           45% /

tmpfs            2,4G         1,1M  2,4G            1% /dev/shm

tmpfs            5,0M         4,0K  5,0M            1% /run/lock

/dev/sda2        512M         6,1M  506M            2% /boot/efi

tmpfs            485M         112K  485M            1% /run/user/1000

/dev/sdb1        4,9G          24K  4,6G            1% /mnt/sdb1
```

- Сменил владельца
  
```
midly@midly-VirtualBox:/mnt$ ls -la

итого 12

drwxr-xr-x  3 root     root     4096 июн  6 01:01 .

drwxr-xr-x 20 root     root     4096 апр 17 20:22 ..

drwxr-xr-x  3 postgres postgres 4096 июн  6 00:58 sdb1
```

- Переместил каталог /var/lib/postgresql/15 в /mnt/sdb1/data - сделано
  При запуске кластера ошибка, потому что каталог /var/lib/postgresql/15/main пустой

- Запустили и проверяем кластер

```
midly@midly-VirtualBox:~$ sudo pg_lsclusters

Ver Cluster Port Status Owner    Data directory              Log file

15  main    5433 online postgres /mnt/sdb1/data/main         /var/log/postgresql/postgresql-15-main.log
```

- Проверяем нашу таблицу
  
```
midly@midly-VirtualBox:~$ sudo -u postgres psql


psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), server 14.12 (Ubuntu 14.12-1.pgdg22.04+1))

Type "help" for help.



postgres=# \l

                                                 List of databases

   Name    |  Owner   | Encoding |   Collate   |    Ctype    | ICU Locale | Locale Provider |   Access privileges   

-----------+----------+----------+-------------+-------------+------------+-----------------+-----------------------

 postgres  | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            | 

 template0 | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            | =c/postgres          +

           |          |          |             |             |            |                 | postgres=CTc/postgres

 template1 | postgres | UTF8     | ru_RU.UTF-8 | ru_RU.UTF-8 |            | libc            | =c/postgres          +

           |          |          |             |             |            |                 | postgres=CTc/postgres

(3 rows)



postgres=# \dt

          List of relations

 Schema |  Name   | Type  |  Owner   

--------+---------+-------+----------

 public | test    | table | postgres

(1 rows)



postgres=# select * from test;

 c1 

----

 1

(1 row)



postgres=# 


