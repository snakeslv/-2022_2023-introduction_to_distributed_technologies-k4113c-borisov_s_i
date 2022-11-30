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





