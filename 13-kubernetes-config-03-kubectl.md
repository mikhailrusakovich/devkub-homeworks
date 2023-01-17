# Домашнее задание к занятию "13.3 работа с kubectl"
## Задание 1: проверить работоспособность каждого компонента
Для проверки работы можно использовать 2 способа: port-forward и exec. Используя оба способа, проверьте каждый компонент:
* сделайте запросы к бекенду;
* сделайте запросы к фронту;
* подключитесь к базе данных.

```commandline
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get all
NAME                                      READY   STATUS    RESTARTS      AGE
pod/nfs-server-nfs-server-provisioner-0   1/1     Running   1 (25h ago)   26h
pod/pod-int-volumes                       2/2     Running   2 (25h ago)   26h
pod/prod-b-v2-687cbbbc85-8xhvq            1/1     Running   1 (25h ago)   26h
pod/prod-b-v2-687cbbbc85-r8855            1/1     Running   1 (25h ago)   26h
pod/prod-f-v2-547768bfb5-dl5fj            1/1     Running   1 (25h ago)   26h

NAME                                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                                                                                     AGE
service/kubernetes                          ClusterIP   10.96.0.1       <none>        443/TCP                                                                                                     26h
service/nfs-server-nfs-server-provisioner   ClusterIP   10.96.111.24    <none>        2049/TCP,2049/UDP,32803/TCP,32803/UDP,20048/TCP,20048/UDP,875/TCP,875/UDP,111/TCP,111/UDP,662/TCP,662/UDP   26h
service/prod-b-v2                           NodePort    10.111.108.61   <none>        8080:32706/TCP                                                                                              26h
service/prod-f-v2                           NodePort    10.101.86.46    <none>        8080:32528/TCP                                                                                              26h

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/prod-b-v2   2/2     2            2           26h
deployment.apps/prod-f-v2   1/1     1            1           26h

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/prod-b-v2-687cbbbc85   2         2         2       26h
replicaset.apps/prod-f-v2-547768bfb5   1         1         1       26h

NAME                                                 READY   AGE
statefulset.apps/nfs-server-nfs-server-provisioner   1/1     26h

Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl port-forward deployment/prod-f-v2 :8080
Forwarding from 127.0.0.1:62101 -> 8080
Forwarding from [::1]:62101 -> 8080
Handling connection for 62232
Mikhails-MBP:devkub-homeworks mikhailrusakovich$ curl localhost:62232
<!doctype html><html lang="en"><head><meta charset="utf-8"/><link rel="shortcut icon" href="/favicon.ico"/><meta name="viewport" content="width=device-width,initial-scale=1"/><meta name="theme-color" content="#000000"/><link rel="manifest" href="/manifest.json"/><title>Product Inventory</title><link href="/static/css/main.a83f6bd7.chunk.css" rel="stylesheet"></head><body><noscript>You need to enable JavaScript to run this app.</noscript><div id="root"></div><script>!function(l){function e(e){for(var r,t,n=e[0],o=e[1],u=e[2],f=0,i=[];f<n.length;f++)t=n[f],p[t]&&i.push(p[t][0]),p[t]=0;for(r in o)Object.prototype.hasOwnProperty.call(o,r)&&(l[r]=o[r]);for(s&&s(e);i.length;)i.shift()();return c.push.apply(c,u||[]),a()}function a(){for(var e,r=0;r<c.length;r++){for(var t=c[r],n=!0,o=1;o<t.length;o++){var u=t[o];0!==p[u]&&(n=!1)}n&&(c.splice(r--,1),e=f(f.s=t[0]))}return e}var t={},p={1:0},c=[];function f(e){if(t[e])return t[e].exports;var r=t[e]={i:e,l:!1,exports:{}};return l[e].call(r.exports,r,r.exports,f),r.l=!0,r.exports}f.m=l,f.c=t,f.d=function(e,r,t){f.o(e,r)||Object.defineProperty(e,r,{enumerable:!0,get:t})},f.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},f.t=function(r,e){if(1&e&&(r=f(r)),8&e)return r;if(4&e&&"object"==typeof r&&r&&r.__esModule)return r;var t=Object.create(null);if(f.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:r}),2&e&&"string"!=typeof r)for(var n in r)f.d(t,n,function(e){return r[e]}.bind(null,n));return t},f.n=function(e){var r=e&&e.__esModule?function(){return e.default}:function(){return e};return f.d(r,"a",r),r},f.o=function(e,r){return Object.prototype.hasOwnProperty.call(e,r)},f.p="/";var r=window.webpackJsonp=window.webpackJsonp||[],n=r.push.bind(r);r.push=e,r=r.slice();for(var o=0;o<r.length;o++)e(r[o]);var s=n;a()}([])</script><script src="/static/js/2.4c8ff4f9.chunk.js"></script><script src="/static/js/main.a9c590ed.chunk.js"></script></body></html>


Mikhails-MBP:devkub-homeworks mikhailrusakovich$ curl localhost:62302
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>NotFoundError: Not Found<br> &nbsp; &nbsp;at /usr/src/app/app.js:22:8<br> &nbsp; &nbsp;at Layer.handle [as handle_request] (/usr/src/app/node_modules/express/lib/router/layer.js:95:5)<br> &nbsp; &nbsp;at trim_prefix (/usr/src/app/node_modules/express/lib/router/index.js:317:13)<br> &nbsp; &nbsp;at /usr/src/app/node_modules/express/lib/router/index.js:284:7<br> &nbsp; &nbsp;at Function.process_params (/usr/src/app/node_modules/express/lib/router/index.js:335:12)<br> &nbsp; &nbsp;at next (/usr/src/app/node_modules/express/lib/router/index.js:275:10)<br> &nbsp; &nbsp;at SendStream.error (/usr/src/app/node_modules/serve-static/index.js:121:7)<br> &nbsp; &nbsp;at SendStream.emit (events.js:198:13)<br> &nbsp; &nbsp;at SendStream.error (/usr/src/app/node_modules/send/index.js:270:17)<br> &nbsp; &nbsp;at SendStream.onStatError (/usr/src/app/node_modules/send/index.js:421:12)</pre>
</body>
</html>

```

## Задание 2: ручное масштабирование

При работе с приложением иногда может потребоваться вручную добавить пару копий. Используя команду kubectl scale, попробуйте увеличить количество бекенда и фронта до 3. Проверьте, на каких нодах оказались копии после каждого действия (kubectl describe, kubectl get pods -o wide). После уменьшите количество копий до 1.

```commandline
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl scale --replicas=3 deployment/prod-b-v2
deployment.apps/prod-b-v2 scaled
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl scale --replicas=3 deployment/prod-f-v2
deployment.apps/prod-f-v2 scaled
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get pods
NAME                                  READY   STATUS    RESTARTS      AGE
nfs-server-nfs-server-provisioner-0   1/1     Running   1 (25h ago)   26h
pod-int-volumes                       2/2     Running   2 (25h ago)   26h
prod-b-v2-687cbbbc85-8xhvq            1/1     Running   1 (25h ago)   26h
prod-b-v2-687cbbbc85-ft4zh            1/1     Running   0             25s
prod-b-v2-687cbbbc85-r8855            1/1     Running   1 (25h ago)   26h
prod-f-v2-547768bfb5-847pj            1/1     Running   0             11s
prod-f-v2-547768bfb5-dl5fj            1/1     Running   1 (25h ago)   26h
prod-f-v2-547768bfb5-t5xbl            1/1     Running   0             11s


Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl describe deployment prod-b-v2
Name:                   prod-b-v2
Namespace:              default
CreationTimestamp:      Mon, 16 Jan 2023 11:25:23 -0500
Labels:                 app=ecommerce
                        tier=back-v2
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=ecommerce,tier=back-v2
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=ecommerce
           tier=back-v2
  Containers:
   prod-b-v2:
    Image:      chrischinchilla/humanitech-product-be
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      DATABASE_HOST:      postgres
      DATABASE_NAME:      product
      DATABASE_PASSWORD:  pr0dr0b0t
      DATABASE_USER:      product_robot
      DATABASE_PORT:      5432
    Mounts:
      /mnt/nfs from data (rw)
  Volumes:
   data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  shared
    ReadOnly:   false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   prod-b-v2-687cbbbc85 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  26h   deployment-controller  Scaled up replica set prod-b-v2-687cbbbc85 to 2
  Normal  ScalingReplicaSet  58s   deployment-controller  Scaled up replica set prod-b-v2-687cbbbc85 to 3


Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl describe deployment prod-f-v2
Name:                   prod-f-v2
Namespace:              default
CreationTimestamp:      Mon, 16 Jan 2023 11:25:23 -0500
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=ecommerce,tier=front-v2
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=ecommerce
           tier=front-v2
  Containers:
   client:
    Image:      chrischinchilla/humanitech-product-fe
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      PROD_B_V2_SERVER_URL:  prod-b-v2
    Mounts:
      /mnt/nfs from data (rw)
  Volumes:
   data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  shared
    ReadOnly:   false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   prod-f-v2-547768bfb5 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  26h   deployment-controller  Scaled up replica set prod-f-v2-547768bfb5 to 1
  Normal  ScalingReplicaSet  81s   deployment-controller  Scaled up replica set prod-f-v2-547768bfb5 to 3


Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl scale --replicas=1 deployment/prod-f-v2
deployment.apps/prod-f-v2 scaled
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl scale --replicas=1 deployment/prod-b-v2
deployment.apps/prod-b-v2 scaled
Mikhails-MBP:13-kubernetes-02 mikhailrusakovich$ kubectl get pods
NAME                                  READY   STATUS        RESTARTS      AGE
nfs-server-nfs-server-provisioner-0   1/1     Running       1 (25h ago)   26h
pod-int-volumes                       2/2     Running       2 (25h ago)   26h
prod-b-v2-687cbbbc85-8xhvq            1/1     Running       1 (25h ago)   26h
prod-b-v2-687cbbbc85-ft4zh            1/1     Terminating   0             2m56s
prod-b-v2-687cbbbc85-r8855            1/1     Terminating   1 (25h ago)   26h
prod-f-v2-547768bfb5-847pj            1/1     Terminating   0             2m42s
prod-f-v2-547768bfb5-dl5fj            1/1     Running       1 (25h ago)   26h
prod-f-v2-547768bfb5-t5xbl            1/1     Terminating   0             2m42s

```
---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
