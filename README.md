# SelectelTest

## Тестовое задание на стажировку системным администратором

## 1. 

awk '{bytes[$1]+=$NF} END {for (ip in bytes) print bytes[ip], "bytes for", ip}' access.log

## 2. 

lsof -nP -iTCP -sTCP:ESTABLISHED

## 3. 

Сервис был недоступен в связи с исчерпанием лимита соединений MySQL и перегрузкой по потокам и продлилась примерно 13:00 до 13:20
  - MySQL Connections - резко возросло и достигло лимита 3000
  - Threads running - почти все потоки активны => БД перегружена
  - MySQL Questions - резкий спад кол-ва запросов => новые запросы не принимаются => клиенты не получают соединение с БД
  - Thread Cache - кол-во закешированных потоков падает до минимума => MySQL не справляется с созданием/обслуживанием новых потоков

## 4.

### Исходный
FROM ubuntu:latest # latest не гарантирует воспроизводимость сборки, лучше зафиксировать версию
MAINTAINER MyCompany # устаревшая инструкция
COPY . /var/www/html # копирование контекста до установки пакетов
RUN apt-get update -y # кэш apt сохраняется => увеличивается размер образа / -y для update не нужен
RUN apt-get install -y nginx # отдельный слой = лишний размер / не очищается /var/lib/apt/lists
CMD ["nginx", "-g", "daemon off;"] 

### Исправленный
FROM ubuntu:22.04  
LABEL maintainer="MyCompany"  
RUN apt-get update \ 
  && apt-get install -y nginx \ 
  && rm -rf /var/lib/apt/lists/*  
COPY ./html /var/www/html  
CMD ["nginx", "-g", "daemon off;"]

## 5.

  - Проверяю мониторинг (HTTP checks, latency, error rate)
  - Проверяю, один ли узел недоступен или несколько
  - Убираю проблемный сервер из балансировщика (LB / DNS / Ingress)     
  - Переключаю трафик на здоровые ноды      
  - Если узел единственный - поднимаю временный replacement (VM / Pod)
  - ping server ssh server uptime df -h free -m
  - systemctl status nginx ps aux | grep nginx ss -lntp | grep :80
  - nginx -t systemctl restart nginx
  - tail -n 100 /var/log/nginx/error.log
  - journalctl -xe dmesg | tail
  - Проверяю внешние факторы (Firewall / iptables / сертификаты / Backend-сервисы / DDoS)
  - Если узел недоступен, пересоздаю VM / контейнер либо восстанавливаю IaC

## 6.

  - убедиться, что образ доступен Kubernetes
  - см. Deployment.yaml [https://google.com]
  - см. Service.yaml
  - см. Access.yaml
  - см. Scaling.yaml


