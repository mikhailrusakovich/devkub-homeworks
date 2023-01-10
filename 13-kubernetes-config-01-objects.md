# Домашнее задание к занятию "13.1 контейнеры, поды, deployment, statefulset, services, endpoints"
Настроив кластер, подготовьте приложение к запуску в нём. Приложение стандартное: бекенд, фронтенд, база данных. Его можно найти в папке 13-kubernetes-config.

## Задание 1: подготовить тестовый конфиг для запуска приложения
Для начала следует подготовить запуск приложения в stage окружении с простыми настройками. Требования:
* под содержит в себе 2 контейнера — фронтенд, бекенд;
* регулируется с помощью deployment фронтенд и бекенд;
* база данных — через statefulset.

```commandline
Mikhails-MBP:13-kubernetes-01 mikhailrusakovich$ kubectl get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/postgres-db-0               1/1     Running   0          90s
pod/stagepod-76bf967c59-dss2z   2/2     Running   0          104s

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes       ClusterIP      10.96.0.1      <none>        443/TCP          2m46s
service/postgres-db-lb   LoadBalancer   10.100.84.16   <pending>     5432:32012/TCP   90s
service/stageservice     NodePort       10.97.56.136   <none>        80:30080/TCP     104s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/stagepod   1/1     1            1           104s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/stagepod-76bf967c59   1         1         1       104s

NAME                           READY   AGE
statefulset.apps/postgres-db   1/1     90s
```

## Задание 2: подготовить конфиг для production окружения
Следующим шагом будет запуск приложения в production окружении. Требования сложнее:
* каждый компонент (база, бекенд, фронтенд) запускаются в своем поде, регулируются отдельными deployment’ами;
* для связи используются service (у каждого компонента свой);
* в окружении фронта прописан адрес сервиса бекенда;
* в окружении бекенда прописан адрес сервиса базы данных.

```commandline
NAME                            READY   STATUS    RESTARTS   AGE
pod/postgres-0                  1/1     Running   0          59s
pod/prod-b-64958b7c5d-bghdd     1/1     Running   0          72s
pod/prod-b-64958b7c5d-gncxg     1/1     Running   0          72s
pod/prod-f-6cdbdb8c6f-l2mml     1/1     Running   0          64s

NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/kubernetes       ClusterIP      10.96.0.1        <none>        443/TCP          4m55s
service/postgres         NodePort       10.96.85.117     <none>        5432:31581/TCP   59s
service/prod-b           NodePort       10.101.98.21     <none>        8080:32413/TCP   72s
service/produ-f          NodePort       10.104.222.175   <none>        8080:31165/TCP   64s

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/prod-b     2/2     2            2           72s
deployment.apps/prod-f     1/1     1            1           64s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/prod-b-64958b7c5d     2         2         2       72s
replicaset.apps/prod-f-6cdbdb8c6f     1         1         1       64s

NAME                           READY   AGE
statefulset.apps/postgres      1/1     59s

```

## Задание 3 (*): добавить endpoint на внешний ресурс api
Приложению потребовалось внешнее api, и для его использования лучше добавить endpoint в кластер, направленный на это api. Требования:
* добавлен endpoint до внешнего api (например, геокодер).

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

В качестве решения прикрепите к ДЗ конфиг файлы для деплоя. Прикрепите скриншоты вывода команды kubectl со списком запущенных объектов каждого типа (pods, deployments, statefulset, service) или скриншот из самого Kubernetes, что сервисы подняты и работают.

---
