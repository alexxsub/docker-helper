# Примеры запуска различных СУБД и инструментов в контейнерах

## Установка

Ставим [Docker Desktop](https://www.docker.com/products/docker-desktop/)


# Основные команды
удалить контейнер
```
docker-compose rm -v <имя контейнера>
```
показать логи в консоль
```
docker-compose logs <имя контейнера>
```
скачать образы и запустить контейнеры
```
docker-compose -f <имя yml> up -d
```
остановить контейнеры
```
docker-compose -f <имя yml> down
```
остановить контейнер
```
docker-compose stop <имя контейнера>
```
запустить контейнер
```
docker-compose up <имя контейнера>
```

# Запуск web сервера на базе [nginx](https://nginx.org/ru/)
app - папка с приложением, nginx-config\default.config-файл настроки
```
$ docker-compose -f nginx.yml up -d
```

# Запуск различных баз данных
## [MySQL](https://www.postgresql.org/)
```
$ docker-compose -f mysql.yml up -d
```
http://localhost:81 - WEB клиент phpmyadmin

## [PostgreSQL](https://www.postgresql.org/)
```
$ docker-compose -f postgre.yml up -d
```
http://localhost:82 - WEB клиент PGadmin


## [MongoDB](https://www.mongodb.com/)
```
$ docker-compose -f mongodb.yml up -d
```
http://localhost:84 - WEB клиент Mongo Express

## [ClickHouse](https://clickhouse.com) 
```
$ docker-compose -f clickhouse.yml up -d
```
http://localhost:85 - WEB клиент ClickHouse

## [InfluxDB](https://www.influxdata.com/) + [Grafana](https://grafana.com/)
```
$ docker-compose -f influxdb.yml up -d
```
http://localhost:86 WEB клиент InfluxDB http://localhost:3001  Grafana


## [Redis](https://redis.io/)
```
$ docker-compose -f redis.yml up -d
```

http://localhost:83 - WEB клиент


установка пароля
```
services:
redis:
...
  environment:
    - REDIS_PASSWORD=password123
...
```
установка порта
```
services:
redis:
...
  environment:
    - REDIS_PORT_NUMBER=7000
  ...
  ports:
    - '7000:7000'
....
```
конфиг файл
```
services:
redis:
...
  volumes:
    - /path/to/your_redis.conf:/opt/bitnami/redis/mounted-etc/redis.conf
    - /path/to/redis-persistence:/bitnami/redis/data
..
```
репликация
```
version: '3'

services:
  redis-master:
    image: 'bitnami/redis:latest'
    ports:
      - '6379'
    environment:
      - REDIS_REPLICATION_MODE=master
      - REDIS_PASSWORD=my_master_password
    volumes:
      - '/path/to/redis-persistence:/bitnami'

  redis-replica:
    image: 'bitnami/redis:latest'
    ports:
      - '6379'
    depends_on:
      - redis-master
    environment:
      - REDIS_REPLICATION_MODE=slave
      - REDIS_MASTER_HOST=redis-master
      - REDIS_MASTER_PORT_NUMBER=6379
      - REDIS_MASTER_PASSWORD=my_master_password
      - REDIS_PASSWORD=my_replica_password
```

```
$ docker-compose up --detach --scale redis-master=1 --scale redis-secondary=3
```

SSL
```
services:
redis:
...
  environment:
    ...
    - REDIS_TLS_ENABLED=yes
    - REDIS_TLS_CERT_FILE=/opt/bitnami/redis/certs/redis.crt
    - REDIS_TLS_KEY_FILE=/opt/bitnami/redis/certs/redis.key
    - REDIS_TLS_CA_FILE=/opt/bitnami/redis/certs/redisCA.crt
  ...
  volumes:
    - /path/to/certs:/opt/bitnami/redis/certs
    - /path/to/redis-persistence:/bitnami/redis/data
```
