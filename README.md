# Wiki

 Сервис для документации
 
 Развернут в виде docker-контейнеров `nginx`, `wiki` и `postgres` и отдельного `node-exporter`-демона

 ## Разворачивание

 С помощью [ansible playbook](wikijs.yaml):

 1. Загружаются необходимые пакеты
 2. Устанавливается Docker
 3. Создается пароль для базы данных Wiki
 4. Копируются все конфиги с машины-хоста (лежат в текущем репозитории)
 5. Ротируются все логи из папки `/var/log` с помощью `logrotate` по конфигу [wiki_rotate.conf](https://github.com/koterin/DevOps/blob/master/configs/wiki_rotate.conf); логи контейнеров докера - с помощью [daemon.json](https://github.com/koterin/DevOps/blob/master/configs/daemon.json)
 6. Загружается node-exporter для Prometheus; запускается как сервис-демон с помощью `systemd` по [node_exporter.service](https://github.com/koterin/DevOps/blob/master/configs/node_exporter.service)
 7. Запускается разворачивание контейнеров с помощью [docker-compose](https://github.com/koterin/DevOps/blob/master/configs/docker-compose.yml)

## Docker-compose

Внутри скрипта:

1. Создается `network wikinet`
2. База данных для Wiki - `PostgreSQL 11`
3. Wiki - по образу `ghcr.io/requarks/wiki:latest`
4. nginx - с открытыми портами `80` и `443`

## NGINX

1. Все попытки соединения не по `hostname={hostname}` выдают `444`
2. HTTP-трафик автоматически перенаправляется на HTTPS
3. NGINX слушает wiki на `3000` порту
