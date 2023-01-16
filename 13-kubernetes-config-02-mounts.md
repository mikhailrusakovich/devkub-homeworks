# Домашнее задание к занятию "13.2 разделы и монтирование"
Приложение запущено и работает, но время от времени появляется необходимость передавать между бекендами данные. А сам бекенд генерирует статику для фронта. Нужно оптимизировать это.
Для настройки NFS сервера можно воспользоваться следующей инструкцией (производить под пользователем на сервере, у которого есть доступ до kubectl):
* установить helm: curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```commandline
Mikhails-MBP:13-kubernetes-01 mikhailrusakovich$ sudo curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
Password:
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 11345  100 11345    0     0  57351      0 --:--:-- --:--:-- --:--:-- 60668
Downloading https://get.helm.sh/helm-v3.10.3-darwin-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm
```
* добавить репозиторий чартов: helm repo add stable https://charts.helm.sh/stable && helm repo update
```commandline
Mikhails-MBP:13-kubernetes-01 mikhailrusakovich$ helm repo add stable https://charts.helm.sh/stable && helm repo update
"stable" has been added to your repositories
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
```
* установить nfs-server через helm: helm install nfs-server stable/nfs-server-provisioner
```commandline
Mikhails-MBP:13-kubernetes-01 mikhailrusakovich$ helm install nfs-server stable/nfs-server-provisioner
WARNING: This chart is deprecated
NAME: nfs-server
LAST DEPLOYED: Mon Jan 16 11:17:07 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The NFS Provisioner service has now been installed.

A storage class named 'nfs' has now been created
and is available to provision dynamic volumes.

You can use this storageclass by creating a `PersistentVolumeClaim` with the
correct storageClassName attribute. For example:

    ---
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
      name: test-dynamic-volume-claim
    spec:
      storageClassName: "nfs"
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 100Mi

```

В конце установки будет выдан пример создания PVC для этого сервера.

## Задание 1: подключить для тестового конфига общую папку
В stage окружении часто возникает необходимость отдавать статику бекенда сразу фронтом. Проще всего сделать это через общую папку. Требования:
* в поде подключена общая папка между контейнерами (например, /static);
* после записи чего-либо в контейнере с беком файлы можно получить из контейнера с фронтом.

```commandline
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl apply -f pod-volumes.yml 
pod/pod-int-volumes created
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get pods,pv,pvc
NAME                                      READY   STATUS              RESTARTS   AGE
pod/nfs-server-nfs-server-provisioner-0   1/1     Running             0          3m4s
pod/pod-int-volumes                       0/2     ContainerCreating   0          12s
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get pods,pv,pvc
NAME                                      READY   STATUS    RESTARTS   AGE
pod/nfs-server-nfs-server-provisioner-0   1/1     Running   0          3m11s
pod/pod-int-volumes                       2/2     Running   0          19s

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec pod-int-volumes -c busybox -- sh -c 'echo "test" > /tmp/cache/ping.txt'
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec pod-int-volumes -c busybox -- ls -la /tmp/cache
total 12
drwxrwxrwx    2 root     root          4096 Jan 16 16:20 .
drwxrwxrwt    1 root     root          4096 Jan 16 16:20 ..
-rw-r--r--    1 root     root             5 Jan 16 16:20 ping.txt

Read

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec pod-int-volumes -c nginx -- ls -la /static
total 12
drwxrwxrwx 2 root root 4096 Jan 16 16:20 .
drwxr-xr-x 1 root root 4096 Jan 16 16:20 ..
-rw-r--r-- 1 root root    5 Jan 16 16:20 ping.txt


Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec pod-int-volumes -c nginx -- sh -c 'cat /static/ping.txt'
test

```

## Задание 2: подключить общую папку для прода
Поработав на stage, доработки нужно отправить на прод. В продуктиве у нас контейнеры крутятся в разных подах, поэтому потребуется PV и связь через PVC. Сам PV должен быть связан с NFS сервером. Требования:
* все бекенды подключаются к одному PV в режиме ReadWriteMany;
* фронтенды тоже подключаются к этому же PV с таким же режимом;
* файлы, созданные бекендом, должны быть доступны фронту.

```commandline
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl apply -f prod.yml 
persistentvolumeclaim/shared created

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get pvc
NAME     STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
shared   Bound    pvc-a66650ad-3372-49bd-9500-eb9fb7995c87   1Gi        RWX            nfs            18s

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl apply -f front-prod.yml -f back-prod.yml 
deployment.apps/prod-f-v2 created
service/prod-f-v2 created
deployment.apps/prod-b-v2 created
service/prod-b-v2 created

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get pod,pvc,deploy
NAME                                      READY   STATUS    RESTARTS   AGE
pod/nfs-server-nfs-server-provisioner-0   1/1     Running   0          9m52s
pod/pod-int-volumes                       2/2     Running   0          7m
pod/prod-b-v2-687cbbbc85-8xhvq            1/1     Running   0          96s
pod/prod-b-v2-687cbbbc85-r8855            1/1     Running   0          96s
pod/prod-f-v2-547768bfb5-dl5fj            1/1     Running   0          96s

NAME                           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/shared   Bound    pvc-a66650ad-3372-49bd-9500-eb9fb7995c87   1Gi        RWX            nfs            3m45s

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/prod-b-v2   2/2     2            2           96s
deployment.apps/prod-f-v2   1/1     1            1           96s

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec prod-b-v2-687cbbbc85-8xhvq -c prod-b-v2 -- ls -la /mnt/nfs
total 8
drwxrwsrwx 2 root root 4096 Jan 16 16:23 .
drwxr-xr-x 1 root root 4096 Jan 16 16:26 ..
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec prod-b-v2-687cbbbc85-8xhvq -c prod-b-v2 -- sh -c 'echo "ping2" > /mnt/nfs/ping2.txt'
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec prod-b-v2-687cbbbc85-8xhvq -c prod-b-v2 -- ls -la /mnt/nfs
total 12
drwxrwsrwx 2 root root 4096 Jan 16 16:28 .
drwxr-xr-x 1 root root 4096 Jan 16 16:26 ..
-rw-r--r-- 1 root root    6 Jan 16 16:28 ping2.txt

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec prod-b-v2-687cbbbc85-8xhvq -c prod-b-v2 -- cat /mnt/nfs/ping2.txt
ping2

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl exec prod-f-v2-547768bfb5-dl5fj -c client -- ls -la /mnt/nfs
total 12
drwxrwsrwx 2 root root 4096 Jan 16 16:28 .
drwxr-xr-x 1 root root 4096 Jan 16 16:26 ..
-rw-r--r-- 1 root root    6 Jan 16 16:28 ping2.txt

```
---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
