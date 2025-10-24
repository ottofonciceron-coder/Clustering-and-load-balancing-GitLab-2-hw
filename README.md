#  Домашнее задание к занятию 2 "Кластеризация и балансировка нагрузки" - Марчук Кирилл



### Задание 1 Запустите два simple python сервера на своей виртуальной машине на разных портах
Установите и настройте HAProxy
Настройте балансировку Round-robin на 4 уровне.



1. `Запускаем 2 простоых Python-серверов web1 и web2`
2. `Настраиваем HAProxy.cfg`
3. `Делаем Тест балансировки`



```
#HAProxy.cfg
global
    log /dev/log    local0
    log /dev/log    local1 notice
    daemon
    maxconn 256

defaults
    log     global
    mode    tcp
    option  dontlognull
    timeout connect 5s
    timeout client  30s
    timeout server  30s

frontend http_front
    bind *:8080
    mode tcp
    default_backend http_back

backend http_back
    mode tcp
    balance roundrobin
    option tcp-check
    server web1 84.252.133.46:8001 check
    server web2 158.160.182.246:8002 check

```


![zadanie1](https://github.com/ottofonciceron-coder/Clustering-and-load-balancing-GitLab-2-hw/blob/main/zadanie%201.png)`

---

### Задание 2 Запустите три simple python сервера на своей виртуальной машине на разных портах
Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
HAproxy должен балансировать только тот http-трафик, который адресован домену example.local



1. `Запускаем 3 простых Python-серверов web1,web2 и web3`
2. `Настраиваем HAProxy.cfg`
3. `Делаем проверку работы с доменом example.local`
4. `Делаем проверку работы без домена`


```
#HAProxy.cfg 2

global
    log /dev/log local0
    maxconn 2048
    daemon

defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5s
    timeout client  30s
    timeout server  30s

frontend http_front
    bind *:8080
    mode http
    acl is_example hdr(host) -i example.local
    use_backend example_backend if is_example
    default_backend default_backend

backend example_backend
    mode http
    balance roundrobin
    # Weighted Round Robin
    server web1 127.0.0.1:8001 weight 2 check
    server web2 127.0.0.1:8002 weight 3 check
    server web3 127.0.0.1:8003 weight 4 check

backend default_backend
    mode http
    balance roundrobin
    # Если домен не example.local то...
    server fallback 127.0.0.1:8001 check

```

![zadanie2](https://github.com/ottofonciceron-coder/Clustering-and-load-balancing-GitLab-2-hw/blob/main/Проверка%20example%20local.png)`

![zadanie2](https://github.com/ottofonciceron-coder/Clustering-and-load-balancing-GitLab-2-hw/blob/main/Проверка%20без%20домена.png)`


---
