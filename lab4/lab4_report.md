# Лабораторная работа №4 "Сети связи в Minikube, CNI и CoreDNS"

## Общая информация

University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Borisov Svyatoslav Igorevich

Lab: Lab4

Date of create: 27.11.2022

Date of finished: 

## Ход работы

### Запуск кластера minikube

Запустим кластер minikube с 2мя нодами и плагином CNI calico с помощью следующей команды:

```bash
minikube start --driver=docker -p multinode-cluster --network-plugin=cni --cni=calico --nodes=2 --kubernetes-version=v1.24.0
```

Проверим количество созданных нод:

```bash
kubectl -- get nodes
```

Вывод консоли:

![image](https://user-images.githubusercontent.com/44950206/204851520-df1bbe9c-398e-4439-a41e-8207c64e5566.png)

Убедимся, что CNI calico запустился успешно. Для этого проверим состояние подов с меткой calico-node с помощью команды:

```bash
kubectl get po -n kube-system -l=k8s-app=calico-node
```

Вывод консоли:

![image](https://user-images.githubusercontent.com/44950206/204851642-7f7d979d-0379-4059-85ea-0f894e51ecd6.png)

Убедились что запуск кластера с 2мя нодами и плагином CNI calico произошел успешно.

### Назначение IP адресов

Во время инициализации кластера minikube c CNI calico, был создан ресурс IPPool - `default-ipv4-ippool`. Он не имеет конкретных правил по назначению ip-адресов на ноды, поэтому удаляем его, чтобы он не машал дальнейшей работе.

Удаляем дефолтный IP пул с помощью команды:

```bash
calicoctl delete ippools default-ipv4-ippool
```

Добавим labels на ноды в соотвествии с их географическим расположением. На основе данных labels будут созданы манифесты для новых IPPools.

Добавление labels для нод:

```bash
kubectl label nodes multinode-cluster zone=east
kubectl label nodes multinode-cluster-m02 zone=west
```

![image](https://user-images.githubusercontent.com/44950206/204861128-80dfc9ff-5741-4d90-bf84-609a557a0478.png)

После добавления labels, создааем манифесты для создания IPPools - [ippools.yaml](ippools.yaml).

IPPool `region-east-ippool` будет назначать подам IP-адреса из адресного пространства `10.244.0.0/24`, находящихся на ноде с меткой `zone=east`.

IPPool `region-west-ippool` будет назначать подам IP-адреса из адресного пространства `10.244.1.0/24`, находящихся на ноде с меткой `zone=west`.

Разворачиваем IPPools в кластере с помощью команды:

```bash
calicoctl create -f ippools.yaml
```

![image](https://user-images.githubusercontent.com/44950206/204861248-fe8fe99a-f83c-45bb-8972-3eeb0b0a96ce.png)

### Проверка назначения IP адресов

Для проверки созданных IP-пулов развернем тестовое приложение и посмотрим, на каких нодах развернулись поды и какие IP-адреса этим подам были присвоены.

Были разработан манифест для создания Deployemnt'а - [deployment.yaml](deployment.yaml).

А также манифест для создания сервиса типа LoadBalancer - [service.yaml](service.yaml)

Разврачиваем Deployment с помощью команды:

```bash
kubectl apply -f deployment.yaml
```

И Service с помощью команды:

```bash
kubectl apply -f service.yaml
```

Теперь переходим к проверке IP-адресов. Получим информацию о подах с помощью команды:

```bash
kubectl get po -o wide
```

Вывод консоли:

![image](https://user-images.githubusercontent.com/44950206/204867703-cd2863bc-a2e1-4276-b0ee-e622be887936.png)

Исходя из консольного вывода, видно, что один под разместился на ноде `multinode-cluster`, которой была присвоена метка `zone=east`, и получил IP-адрес из адресного пространства: `10.244.0.0/24`. Второй же под запустился на ноде `multinode-cluster-m02` с меткой `zone=west` и получил IP-адрес из пула адресов `10.244.1.0/24`.

### Тестирование приложения в браузере

Теперь запустим туннель minikube и проверим работу приложения в браузере.

Произведем запуск туннеля minikube с помощью команды:

```bash
minikube tunnel --profile=multinode-cluster
```

// image 2

// image 3

### Схема организации контейнеров и сервисов

// image 4



