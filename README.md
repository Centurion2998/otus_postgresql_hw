- Домашнее задание : Нагрузочное тестирование и тюнинг PostgreSQL

- Развернул ВМ , поставил на нее PG
- Подал нагрузку при стандартных настройках
  
```
postgres@midly-VirtualBox:~$ pgbench -c50 -j 2 -P 10 -T 600 -U postgres postgres

pgbench (14.12 (Ubuntu 14.12-1.pgdg22.04+1))

starting vacuum...end.

progress: 10.0 s, 297.3 tps, lat 159.553 ms stddev 114.705

progress: 20.0 s, 365.8 tps, lat 136.948 ms stddev 74.436

progress: 30.0 s, 270.4 tps, lat 185.028 ms stddev 121.551

progress: 40.0 s, 275.7 tps, lat 180.956 ms stddev 96.174

progress: 50.0 s, 277.4 tps, lat 179.672 ms stddev 100.787

progress: 60.0 s, 252.0 tps, lat 199.493 ms stddev 89.505

progress: 70.0 s, 116.2 tps, lat 415.507 ms stddev 297.976

progress: 80.0 s, 116.3 tps, lat 435.070 ms stddev 313.915

progress: 90.0 s, 116.2 tps, lat 428.314 ms stddev 231.279

progress: 100.0 s, 88.2 tps, lat 553.245 ms stddev 466.016

progress: 110.0 s, 93.6 tps, lat 541.645 ms stddev 487.981

progress: 120.0 s, 83.6 tps, lat 592.654 ms stddev 415.130

progress: 130.0 s, 78.0 tps, lat 645.969 ms stddev 456.613

progress: 140.0 s, 83.9 tps, lat 599.189 ms stddev 518.658

progress: 150.0 s, 99.1 tps, lat 503.933 ms stddev 447.666

progress: 160.0 s, 75.4 tps, lat 653.979 ms stddev 470.606

progress: 170.0 s, 85.8 tps, lat 587.378 ms stddev 442.380

progress: 180.0 s, 99.3 tps, lat 509.278 ms stddev 322.351

progress: 190.0 s, 78.3 tps, lat 640.220 ms stddev 415.696

progress: 200.0 s, 73.3 tps, lat 662.056 ms stddev 422.106

progress: 210.0 s, 83.1 tps, lat 601.519 ms stddev 397.204

progress: 220.0 s, 78.5 tps, lat 633.675 ms stddev 453.415

progress: 230.0 s, 73.6 tps, lat 676.481 ms stddev 489.656

progress: 240.0 s, 96.2 tps, lat 536.783 ms stddev 492.162

progress: 250.0 s, 65.6 tps, lat 706.426 ms stddev 691.340

progress: 260.0 s, 75.9 tps, lat 695.328 ms stddev 797.057

progress: 270.0 s, 82.8 tps, lat 604.941 ms stddev 397.313

progress: 280.0 s, 72.7 tps, lat 658.093 ms stddev 468.301

progress: 290.0 s, 65.4 tps, lat 782.913 ms stddev 722.537

progress: 300.0 s, 77.5 tps, lat 653.684 ms stddev 507.492

progress: 310.0 s, 110.8 tps, lat 461.377 ms stddev 388.549

progress: 320.0 s, 129.3 tps, lat 390.262 ms stddev 300.430

progress: 330.0 s, 130.4 tps, lat 373.377 ms stddev 290.222

progress: 340.0 s, 131.0 tps, lat 388.868 ms stddev 269.254

progress: 350.0 s, 116.3 tps, lat 429.145 ms stddev 267.113

progress: 360.0 s, 117.1 tps, lat 428.522 ms stddev 316.607

progress: 370.0 s, 123.0 tps, lat 405.557 ms stddev 285.904

progress: 380.0 s, 114.8 tps, lat 433.962 ms stddev 386.526

progress: 390.0 s, 101.7 tps, lat 486.785 ms stddev 270.162

progress: 400.0 s, 105.0 tps, lat 480.149 ms stddev 329.557

progress: 410.0 s, 100.2 tps, lat 502.610 ms stddev 319.178

progress: 420.0 s, 117.0 tps, lat 424.752 ms stddev 320.092

progress: 430.0 s, 117.1 tps, lat 426.469 ms stddev 274.468

progress: 440.0 s, 106.6 tps, lat 468.932 ms stddev 250.452

progress: 450.0 s, 106.3 tps, lat 471.155 ms stddev 321.084

progress: 460.0 s, 118.0 tps, lat 424.293 ms stddev 263.392

progress: 470.0 s, 93.9 tps, lat 523.208 ms stddev 336.736

progress: 480.0 s, 98.9 tps, lat 501.592 ms stddev 447.535

progress: 490.0 s, 96.2 tps, lat 527.706 ms stddev 338.541

progress: 500.0 s, 79.5 tps, lat 623.046 ms stddev 494.664

progress: 510.0 s, 87.7 tps, lat 572.047 ms stddev 347.578

progress: 520.0 s, 92.6 tps, lat 542.493 ms stddev 349.527

progress: 530.0 s, 100.4 tps, lat 500.430 ms stddev 268.464

progress: 540.0 s, 92.9 tps, lat 537.502 ms stddev 369.271

progress: 550.0 s, 94.4 tps, lat 532.099 ms stddev 389.608

progress: 560.0 s, 92.1 tps, lat 538.638 ms stddev 312.675

progress: 570.0 s, 94.7 tps, lat 530.674 ms stddev 378.930

progress: 580.0 s, 95.7 tps, lat 522.747 ms stddev 416.830

progress: 590.0 s, 87.4 tps, lat 568.053 ms stddev 379.253

progress: 600.0 s, 93.0 tps, lat 527.566 ms stddev 378.098

transaction type: <builtin: TPC-B (sort of)>

scaling factor: 1

query mode: simple

number of clients: 50

number of threads: 2

duration: 600 s

number of transactions actually processed: 69461

latency average = 431.735 ms

latency stddev = 383.491 ms

initial connection time = 403.295 ms

tps = 115.734095 (without initial connection time)

```

- Увеличим значения

```
postgres=# alter system set work_mem='64MB';

ALTER SYSTEM

postgres=# alter system set maintenance_work_mem='512MB';

ALTER SYSTEM

postgres=# alter system set shared_buffers='1GB';

ALTER SYSTEM

postgres=# alter system set wal_buffers='16MB';

ALTER SYSTEM

postgres=# alter system set min_wal_size='512MB';

ALTER SYSTEM

postgres=# alter system set max_wal_size='2GB';

ALTER SYSTEM

postgres=# select pg_reload_conf();

 pg_reload_conf 

----------------

 t

(1 строка)
```

- Проведем повторный запуск

```
postgres@midly-VirtualBox:~$ pgbench -c50 -j 2 -P 10 -T 600 -U postgres postgres

pgbench (14.12 (Ubuntu 14.12-1.pgdg22.04+1))

starting vacuum...end.

progress: 10.0 s, 398.4 tps, lat 119.582 ms stddev 66.338

progress: 20.0 s, 320.8 tps, lat 154.527 ms stddev 82.064

progress: 30.0 s, 246.8 tps, lat 203.847 ms stddev 94.552

progress: 40.0 s, 257.9 tps, lat 186.259 ms stddev 118.602

progress: 50.0 s, 201.3 tps, lat 258.580 ms stddev 192.424

progress: 60.0 s, 152.8 tps, lat 325.156 ms stddev 195.253

progress: 70.0 s, 147.1 tps, lat 326.626 ms stddev 212.586

progress: 80.0 s, 129.8 tps, lat 400.616 ms stddev 290.800

progress: 90.0 s, 161.2 tps, lat 309.033 ms stddev 177.544

progress: 100.0 s, 176.8 tps, lat 280.114 ms stddev 146.775

progress: 110.0 s, 197.5 tps, lat 255.762 ms stddev 124.450

progress: 120.0 s, 162.5 tps, lat 304.204 ms stddev 184.577

progress: 130.0 s, 102.8 tps, lat 478.937 ms stddev 375.249

progress: 140.0 s, 90.9 tps, lat 541.941 ms stddev 344.007

progress: 150.0 s, 123.8 tps, lat 418.681 ms stddev 312.613

progress: 160.0 s, 113.3 tps, lat 419.995 ms stddev 408.120

progress: 170.0 s, 100.0 tps, lat 515.599 ms stddev 678.243

progress: 180.0 s, 102.2 tps, lat 492.774 ms stddev 351.801

progress: 190.0 s, 125.9 tps, lat 394.323 ms stddev 290.605

progress: 200.0 s, 120.0 tps, lat 399.656 ms stddev 355.780

progress: 210.0 s, 71.1 tps, lat 709.956 ms stddev 693.041

progress: 220.0 s, 79.2 tps, lat 649.660 ms stddev 498.132

progress: 230.0 s, 93.9 tps, lat 530.710 ms stddev 432.065

progress: 240.0 s, 77.3 tps, lat 649.893 ms stddev 593.351

progress: 250.0 s, 67.8 tps, lat 727.928 ms stddev 509.009

progress: 260.0 s, 83.7 tps, lat 598.076 ms stddev 451.039

progress: 270.0 s, 72.2 tps, lat 685.980 ms stddev 636.989

progress: 280.0 s, 72.3 tps, lat 695.839 ms stddev 567.257

progress: 290.0 s, 73.1 tps, lat 678.049 ms stddev 473.792

progress: 300.0 s, 54.6 tps, lat 903.085 ms stddev 695.967

progress: 310.0 s, 67.0 tps, lat 757.460 ms stddev 580.841

progress: 320.0 s, 63.8 tps, lat 780.670 ms stddev 549.829

progress: 330.0 s, 55.7 tps, lat 878.114 ms stddev 623.287

progress: 340.0 s, 59.3 tps, lat 863.996 ms stddev 752.494

progress: 350.0 s, 93.7 tps, lat 547.654 ms stddev 446.158

progress: 360.0 s, 93.8 tps, lat 526.932 ms stddev 415.294

progress: 370.0 s, 91.3 tps, lat 545.828 ms stddev 379.136

progress: 380.0 s, 77.4 tps, lat 646.185 ms stddev 542.001

progress: 390.0 s, 94.9 tps, lat 528.578 ms stddev 398.272

progress: 400.0 s, 93.9 tps, lat 540.951 ms stddev 404.701

progress: 410.0 s, 114.9 tps, lat 432.726 ms stddev 242.279

progress: 420.0 s, 110.4 tps, lat 450.384 ms stddev 312.175

progress: 430.0 s, 88.7 tps, lat 557.373 ms stddev 359.635

progress: 440.0 s, 88.5 tps, lat 572.572 ms stddev 422.632

progress: 450.0 s, 63.4 tps, lat 767.850 ms stddev 696.338

progress: 460.0 s, 76.4 tps, lat 665.179 ms stddev 504.236

progress: 470.0 s, 83.6 tps, lat 595.832 ms stddev 465.377

progress: 480.0 s, 62.0 tps, lat 776.140 ms stddev 635.210

progress: 490.0 s, 92.1 tps, lat 570.842 ms stddev 394.543

progress: 500.0 s, 103.9 tps, lat 483.823 ms stddev 354.485

progress: 510.0 s, 100.3 tps, lat 494.386 ms stddev 312.957

progress: 520.0 s, 91.1 tps, lat 549.705 ms stddev 327.530

progress: 530.0 s, 100.5 tps, lat 499.676 ms stddev 345.256

progress: 540.0 s, 88.3 tps, lat 544.880 ms stddev 354.313

progress: 550.0 s, 46.9 tps, lat 1041.293 ms stddev 724.794

progress: 560.0 s, 50.6 tps, lat 1000.670 ms stddev 782.817

progress: 570.0 s, 49.6 tps, lat 943.187 ms stddev 988.656

progress: 580.0 s, 43.5 tps, lat 1224.234 ms stddev 1235.828

progress: 590.0 s, 47.0 tps, lat 1090.123 ms stddev 726.730

progress: 600.0 s, 49.4 tps, lat 977.258 ms stddev 776.162

transaction type: <builtin: TPC-B (sort of)>

scaling factor: 1

query mode: simple

number of clients: 50

number of threads: 2

duration: 600 s

number of transactions actually processed: 65240

latency average = 459.975 ms

latency stddev = 465.804 ms

initial connection time = 406.955 ms

tps = 200.584297 (without initial connection time)
```

- Результат вышел не таким как я ожидал, настройки повлияли на незначительное увелечение с  115.734095 до 200.584297 

- Параметры которые изменили
- work_mem Устанавливает объем памяти, который будет использоваться внутренними операциями сортировки и хэш-таблицами перед записью во временные файлы на диске.
- maintenance_work_mem Устанавливает базовый максимальный объем памяти, который будет использоваться операцией запроса (например, сортировкой или хэш-таблицей) перед записью во временные файлы на диске.
- shared_buffers определяет , сколько памяти выделено серверу для кэширования данных.
- min_wal_size параметр задает минимальный размер журнального сегмента, до которого должен “опуститься” WAL перед переиспользованием.
- max_wal_size параметр устанавливает максимальный размер журнального сегмента.
  
