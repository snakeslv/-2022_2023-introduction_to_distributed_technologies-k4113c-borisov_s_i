# Лабораторная работа №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса."

## Общая информация

University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Borisov Svyatoslav Igorevich

Lab: Lab2

Date of create: 27.11.2022

Date of finished: 

## Ход работы

### Создание Deployment

Для развертывания приложения был создан манифест [deployment.yaml](deployment.yaml). Deployment — это объект Kubernetes, представляющий работающее приложение в кластере. Оно позволяет управлять приложением, его версиями и обновлениями. В объекте Deployment хранится информация о конфигурации подов, количестве необходимых реплик и методе обновления подов в случае изменения их конфигурации.

При развертывании deployment создается еще одна абстракция - ReplicaSet. Она отвечает за описание и контроль за несколькими экземплярами приложений.

Для развертывания deployment используем следующую команду:

```bash
minikube kubectl -- apply -f deployment.yaml
```
### Создание сервиса типа NodePort 

Для предоставления доступа к подам деплоймента `lab2` был создан манифест [service.yaml](service.yaml) для создания сервиса типа NodePort.

Сервисы в Kubernetes позволяют определить набор подов и правила доступа к ним. С помощью сервисов разные части приложения могут "общаться" друг с другом (например, фронтенд с бэкендом).

Для создания описанного в манифесте сервиса была использована следующая команда:

```bash
minikube kubectl -- apply -f service.yaml
```

### Проброс портов и результаты работы

С помощью комманды

```bash
minikube kubectl -- port-forward service/lab2 8888:8080
```
Открываем порт для доступа к сервису и заходим через браузер в приложение:

![image](https://user-images.githubusercontent.com/44950206/204600984-7204f70c-76c5-40f5-8e3c-70aa4af211c1.png)

Имя и IP контейнера не меняются т.к. в качестве сервиса был выбран NodePort.

Развернутый deployemnt:

![image](https://user-images.githubusercontent.com/44950206/204600263-c1820722-189b-407c-bbf9-84da673223e0.png)

Развернутые поды:

![image](https://user-images.githubusercontent.com/44950206/204600323-9d0bf8b6-7366-4951-b8ad-7bd4beb910aa.png)

Развернутый сервис:

![image](https://user-images.githubusercontent.com/44950206/204600344-6c4df162-86e1-4581-948e-f7f9f1a6047b.png)

### Логи контейнеров

Плдучаем логи контейнеров с помощью команды:

```bash
minikube kubectl -- logs <pod_name>
```

Логи подов:

![image](https://user-images.githubusercontent.com/44950206/204601755-b0757633-f4d6-4342-99ca-cffed6978fcc.png)

### Схема организации контейнеров и сервисов

![image](diagram.png)
