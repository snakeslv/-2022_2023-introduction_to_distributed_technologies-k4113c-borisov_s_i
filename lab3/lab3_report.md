# Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

## Общая информация

University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Borisov Svyatoslav Igorevich

Lab: Lab3

Date of create: 27.11.2022

Date of finished: 

## Ход работы

### Создание ConfigMap

С помощью ConfigMap можно определять конфигурационные файлы и переменные, которые в дальнейшем будут использоваться контейнерами с приложениями. 
Таким образом, ConfigMap избавляет от необходимости упаковывать конфиги в docker-образ.

Создаем манифест для создания СonfigMap - [configmap.yaml](configmap.yaml). 

Разворачивем ConfigMap `lab3-config` с помощью команды:

```bash
minikube kubectl -- apply -f configmap.yaml
```

### Создание ReplicaSet и передача значений

Далее был разработан манифест для создания ReplicaSet - [replicaset.yaml](replicaset.yaml)

В поле envFrom указали имя созданного ConfigMap для передачи значений переменных.

Разворачиваем ReplicaSet с помощью команды:

```bash
minikube kubectl -- apply -f replicaset.yaml
```

Также был создан манифест для сервиса типа ClusterIP для обеспечения доступа к подам приложения - [service.yaml](service.yaml)

Разворачиваем Service с помощью команды:

```bash
minikube kubectl -- apply -f service.yaml
```

### Создание ingress-controller

Ingress Controller по сути является балансировщиком нагрузки для Kubernetes. Он позволяет принимать траффик извне кластера и распределять его между подами. 

Для данной работы воспользуемся Nginx Ingress controller, который разворачивается с помощью команды:

```bash
minikube kubectl -- apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.0/deploy/static/provider/cloud/deploy.yaml
```

### Создание TLS сертификата

С помощью утилиты `mkcert` генерируем TLS-сертификат:

```bash
mkcert borisov.com "*.borisov.com" localhost 127.0.0.1
```

Затем импортируем TLS-сертификат в кластер в виде Secret: 

```bash
minikube kubectl -- create secret tls tls-cert --key key.pem --cert cert.pem
```

### Создание Ingress в minikube

Далее был определен манифест для сорздания Ingress - [ingress.yaml](ingress.yaml) 

В нем были описаны dns-имена и правила распределения траффика между подами.

Разворачиваем Ingress с помощью команды:

```bash
minikube kubectl -- apply -f ingress.yaml
```

### Получение доступа к приложению через FQDN

Для получения доступа к сервису `ingress-nginx-controller` необходимо запустить туннель minikube:

```bash
$ minikube tunnel
```

Для того чтобы наше приложение `lab3` было доступно по FQDN на хосте, необходимо внести изменения в файл hosts, добавив следующую строку:

```
127.0.0.1 lab3.borisov.com
```

После этого открываем наше приложения в браузере по адресу https://lab3.borisov.com/

![image](https://user-images.githubusercontent.com/44950206/204838335-e1ab596e-5f23-4de4-b4e1-9c6f6411b8c1.png)

Проверяем наличие TLS-сертификата:

// image2

### Схема организации контейнеров и сервисов

// image 3
