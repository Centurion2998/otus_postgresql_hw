- Домашнее задание : Установка и настройка PostgteSQL в контейнере Docker

- Развернул ВМ Ubuntu 22.04
Ставим на нем докер

```
curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh && rm get-docker.sh && sudo usermod -aG docker $USER && newgrp docker
```

- Cделал каталог /var/lib/postgres
  
```
sudo mkdir /var/lib/postgres
```

- Развернул docker-сеть

```
sudo docker network create pg-net

83de4856d02dd7b3d9f19b0922b572c0599b0fcd5e19839a77fdfe83aa956c81
```

- Подключаем созданную сеть к контейнеру сервера Postgres

```
sudo docker run --name pg-server --network pg-net -e POSTGRES_PASSWORD=postgres -d -p 5432:5432 -v /var/lib/postgres:/var/lib/postgresql/data postgres:15

15: Pulling from library/postgres

09f376ebb190: Pull complete 

119215dfb3e3: Pull complete 

94fccb772ad3: Pull complete 

0fc3acb16548: Pull complete 

d7dba7d03fe8: Pull complete 

898ae395a1ca: Pull complete 

088e651df7e9: Pull complete 

ed155773e5e0: Pull complete 

52df7d12fb73: Pull complete 

bab1ecc22dc9: Pull complete 

1655a257a5b5: Pull complete 

978f02dfc247: Pull complete 

d715d7d9aee0: Pull complete 

b2e9251b2f8d: Pull complete 

Digest: sha256:4b4da96c37fefd6f28c3f58e7470bbc6d2cb34ac5641b9df7221d962eb4bc55d
Status: Downloaded newer image for postgres:15
```

- Запуск отдельного контейнера с клиентом
  
```
sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
```

- Удалил контейнер с сервером , выполнив подключение из контейнера с клиентом к контейнеру с сервером
  Проверил что данные остались на месте

```
sudo docker rm pg-server

sudo docker run -it --rm --network pg-net --name pg-client postgres:15 psql -h pg-server -U postgres
```



```
