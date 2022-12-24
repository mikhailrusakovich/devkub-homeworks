# Домашнее задание к занятию "12.2 Команды для работы с Kubernetes"
Кластер — это сложная система, с которой крайне редко работает один человек. Квалифицированный devops умеет наладить работу всей команды, занимающейся каким-либо сервисом.
После знакомства с кластером вас попросили выдать доступ нескольким разработчикам. Помимо этого требуется служебный аккаунт для просмотра логов.

## Задание 1: Запуск пода из образа в деплойменте
Для начала следует разобраться с прямым запуском приложений из консоли. Такой подход поможет быстро развернуть инструменты отладки в кластере. Требуется запустить деплоймент на основе образа из hello world уже через deployment. Сразу стоит запустить 2 копии приложения (replicas=2). 

Требования:
 * пример из hello world запущен в качестве deployment
 * количество реплик в deployment установлено в 2
 * наличие deployment можно проверить командой kubectl get deployment
 * наличие подов можно проверить командой kubectl get pods

```commandline
Mikhails-MBP:12-02 mikhailrusakovich$ kubectl apply -f helloworld_dep.yaml 
deployment.apps/hello-world created
service/hello-world unchanged

Mikhails-MBP:12-02 mikhailrusakovich$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-world-75b9ff444f-d2plg   1/1     Running   0          12s
hello-world-75b9ff444f-mknl9   1/1     Running   0          12s


```

## Задание 2: Просмотр логов для разработки
Разработчикам крайне важно получать обратную связь от штатно работающего приложения и, еще важнее, об ошибках в его работе. 
Требуется создать пользователя и выдать ему доступ на чтение конфигурации и логов подов в app-namespace.

Требования: 
 * создан новый токен доступа для пользователя
 * пользователь прописан в локальный конфиг (~/.kube/config, блок users)
 * пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>)

```commandline
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /Users/mikhailrusakovich/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Sat, 24 Dec 2022 13:30:22 EST
        provider: minikube.sigs.k8s.io
        version: v1.26.0
      name: cluster_info
    server: https://192.168.59.100:8443
  name: minikube
contexts:
- context:
    cluster: ""
    user: mikhail
  name: mikhail
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Sat, 24 Dec 2022 13:30:22 EST
        provider: minikube.sigs.k8s.io
        version: v1.26.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: mikhail
  user:
    client-key: mikhail.key
- name: mikhail
  user:
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlKS1FJQkFBS0NBZ0VBdDlBM0luQkRKTG9vRHJkOFlHYWxBbi9pSXpIdkRkbnRVRVBXTGZjeXVWSWt5RmJXCnFiMW9lNUQ2VXl6dzFSd3JCa0tYL1hqVFVtcEY2WkVscDhRaStKcXJoYmxyTDhIUTBSNHVkVjlXSUR1NUJGeW8KWEJUeW9LZXN3SUttcXA1RGwvL1V3ZWNDVFFoVXRKa3JxSTFZUFZWU051elNvL0Rzeks1cDNhNUVjNEJ3RVcrNApKd2I3K1dkMmpLNFJoMDdNellnZ1dFd0RQelQrdmtlM3NFZHVDYisrSERjaFFPZUpja0o5VXBuM21DWlNTdlQ3CnkvOXd6VXdwZFRLMVR2Mmd0elRjbEpvQ3AyZnVQZ0RPWW5qdzNrbVBRT0QvejBOaFFycHJGSC9XeDVwTG1ScHEKdS9kbGdQVGozWlk5OW9mOVFCakJTd3dtd0JhMmhHK0JZMTFZbVR1ZkwwcW5pcmdOSTZOZ0RySXVuY0lIc3V5MwpINmxkU3BWbHdEWEVQb1Buam01QzhWYVk5MDFueSttN0tWSGo0YWE1SlR6ZEoreHphTnVlSlNuSVRaL0c1QVFrCmhNUDBPcHdhUGxGeVhQaEJMd2x3SjFBZGVBdjVoUEV0dTF4MWxvajNKcUlkR1FrbHVHWjA4dHdheFVSOXVhMnoKa1FCRlhvYTE1di91WGVYWllqV08zM2dxLzhWOVBMRzVISnp3T3gxaVFZMlJZTVFPTThWNyt2a1JoRzhSVnhnNwpHWXJEckZyRHA0Tk52b2lOQU8rZTNQWjloNDgyaFNIb0dyckRTY0NzK1EzeWdHYVVCOTFwd3ZJNmxvVlp5dWNHCkdhRmgyNzdwZXZDYldCTUs0OTIvcnphT3BrOWgwZEptdXpBQlFBaHAwYkF6aUZGZXovZnNJWkNVN1U4Q0F3RUEKQVFLQ0FnRUFoUW9VL2I0WE90dnA1a3p6bnFwc1FCVGpUZW4wUmlnM1ZXTGtBRHpzMzZrT3Fsbi80TGNRaW03eQpYNFVsZ25seXdvTXNrdThDdEtIcW5CVE1GbE1scEozK0N6bWExT1FKQ1hJNDJnYjR4VGUwdisxNGhiMDdwdi8xClR4akJITGlUMzgzdzVhOFh3VDJJWVhhRFhPbUF0bE9zdjhoRTVSZVpyTU9JS0VUYmttV2h0MTBQQW1CUnU4QWcKMDgyaExqZmdqOW80M3UrVllnZ3ZZUFZ6aEFrUEljdEFCQmY2MzRrMXVCZGJzWTl5M3hMTmVTYzduL2p3WWdiLwo4cTJUQU9zZGlieWU4b2RONkhDV3hSVzhTeFdSOExGNUY5SkNGSGVHUzgrMWRRN2FHM29Yd1dhWFhoaS94TWg2CnJJVHpzQjNaTkswMktlVlBuclJSd2lVYWx4cVc1b2NuNHl6UkYvZlFKdEtjNGdTbDRQWmJDdExDeFN5d3JOcHoKMGk1cnd6Z2FsZDA0eDMyK25vc1Q3WVp0Q0ZWR3hzQ3Bwbk43NU40YmsxTG92TGZoZTFzL0VTQkZWS1E5eFhNNQpiZ0VqT1hCY3NKRWlNM1lqaVJuK0hSVkhEeXFvZXAwdlZOZmQxZ0ZoalZpa09CdXllSGgwbkxEQW5QTUh6WU16CnluSzZJdmJscWZqb0pBVGZQMW9KS1I1RGx6bDNTbmFJODBwS1ZjWGN6WGVGRldyclpkcXFwWGt4Y1lQQkMvWEsKVm9vbm5TVmR0Z2hEbFBQTlZ4VlJyZjhPWXZTSjBzSzdqM1IrUFk4VjNWMnBYZGVjQmVUZi9MZzR2ZlhmOVZjeApVM1NBaTJvRTJiU2dKY1FNWVdRSU5Qd3N0Vm9vdHM1Y3JSZWl3YW54NTF1WmhlZFU2c0VDZ2dFQkFPTElVZHNPCmN4ZDRPRDBEMXV4elFDQjhYeko4aFJhUlFKSERrU05jTEIyTXFkMjBjZGdtVHJKcDQ5NnYrYWsrOWxsK2FNcGQKejFjazRwdG9OSVVZYTlRT1V0QXRldDc4enNDK0h0R1B2QWxmWU5CbmVDb01TTWErUVJQNm80MXdCNEovdWVWNgpwOVlhVDhobjE1RkVyQi9ST29RWUkwdDdiRDFqWnNwaEZpOE5Fc0JSRUc1NXN2YlFvdEgyMnc3MktBZVE4VGZzCitlR2ZWalZTOThUbEw3a1lvenFrWHYzcmFZUUs0KzBHYXZ6QWlkUktwRFpsOFlsZElhZ2pnaUZjMlFCeWFvUEgKYlJnOW5UT2UwSW5abzdUQ1FKK0pBemNyb1hvRUFPSjNkcU5ZWXN1OWNQQ3ZDb2o5dGdITndGRzR3dEVpQWswSwp2U0d5WmZyek1NUkE0VjhDZ2dFQkFNOStzc0M4RWp4M25FdzFSUmc3QnlDSTlQSm5IazQrMkpKai94enoyRGlmClJjd3R6VkRCcXpLWEdnUm5CRmRMaU1uU1g4NjRFNzFMd09HRDNROU1PY016eHkva3FaVVVDYWl5K2F5c2F4aGMKekN4Q3NzdVRlNEszMGtoY2NUU21EQWdoQ21pSkJld3ZTS2ZNNi9qZExacUVLaFBKR1czVFRVWWhpcGNWTXZ2dQpEdkNCOTY2eFdsQjlnRGhPK3NCY05xYjNqQUh2Nm41M25MamZPeGhHOTFmRklOMGI0UVl0TGJpbDhBWGw0ZVF2CmlRdmlpMGpBc0tNYzR3WVRqY0hSdkd5Yk55OGN1QnlSM2dWTXhMTnVBd2lFR2pubTVrMk42VHVXb21IaExLckYKWitiVmNqNm15czFOSlBvUnoxZXBKTWsvOUg3SUtNYllVOW83bGNvdnloRUNnZ0VBUjBReVRRZWViV3F2S2FWeQpQZjM5MGZlanB1YnduK1huaDZjUWppOGlBZXM1V2wyaFJRN012azUrZUhXT25Id1h5SW5yL21RNml5VWhQVHNpCi9neHRua2NlQ1NPeHNDOTcvYUFCYVZPbEFNRURXSnFiYllOTXYzLzhUWDMvTmF4Vi85R0pwcTdEM1ltSk51NUQKRlBpaXFxRUNwWTQ4VVRVcEQ2V1VJTmNmdEl1RU1BR0ppMTRkT21qWU1lbEViOUExUmlOcldtclRIKzhGbFY1TQpWSWk5VllxRGlTTXNZdGsrNEdyWHM1M0hzMFFDVEQ5a21WK3g1cnZvbnNFQjNPQWpwWHRQTTdoUTlVUXZpWkJICjhubjljd2wwYTI0UUg1OUxjRitmczR0ZE1mbk5tajhmSmRPc0dONjUrcGtnN2MyRkUxbC9wWnhSVVN5UjFhbmsKMGxlZE93S0NBUUJlZDFOUWRnOWpZaDFSZ01zbFBmSi91SHEwUlloQm5WRWlUTTVmMHhCMHJ1YXJENHN6SWdrYwpMampWR2tXYXJMUHBGcE14M1JKM2t4Nk1UV2wrUm5qaHl4ZjdVUUozOGJoNENvdXJOcEJIZGpBcVVtOXVTWVhvCnZHSExPZkw4UWtDbURzemJUTTdhZWFoOVpNbGw4dVNKUFhTZXIyYVpYcU1Hczh5Ui9qL0kvTmZtWFhWekhpRjkKRmJZamJLbXluQWp4dVRBUHpiZVh2a01tMDlMbXVhQndZRG9YRUZOQmFaNHdPN1BzSzMzTlVtcjFjejF3Y1BHNgpRVE9HbDY1cE9HQ3RuWGpoUlJUNVJOdXMxQWVWblJkYWNESTBmRkFMclBrcmZPZ080ZUpoR1NDMXNpV2lLbk9ZClJtLzRQZFdGRm1lK0RCVDNCNCt3LzlvbE9BMTVFSXJSQW9JQkFRRFpweFd2cGl1WXEvRE9tZTVKRGdpbVZoc1oKTVZqS0lsOGgrYUtmVndYTlc4SSt6M2F3bm9EakhhUkE4ZWZBZnRaWkpNaUhYUnNiSXBOamR5VDBiTFFHc2JxSAppQ1VTS3JCbUV0QUh0dk9YaENDWjNrY0pEeHFmZ1l3aGFBQXQ4VW9mdnZFSWNrYkRUMnkwVmFRQ2RVN1hia2dqCnlIUnhzaTByaURmUnRsV2Q2KzJsbVEwd0QyQlZTZGIrdjdCTC8rNEF4aGxubVpzSW5YZzZNM0pTRlF5VSt1NEoKZW5UUTFZOWh0WFk5dzhCSUh0dnhyamRlaXk0WUQ4aDA3bEhYM3VVa1I3NXFGaE81ZFpRRzREVE9vMEFOc3NpbQpiYmQwRmxwZWNnaSt4M0NidkpOanZWTzY3RTlCaEZUSnZ3UnB3YzkzWmJUN2wwQ1d1dVJaZlZPU1BmYU4KLS0tLS1FTkQgUlNBIFBSSVZBVEUgS0VZLS0tLS0K
- name: minikube
  user:
    client-certificate: /Users/mikhailrusakovich/.minikube/profiles/minikube/client.crt
    client-key: /Users/mikhailrusakovich/.minikube/profiles/minikube/client.key

```


## Задание 3: Изменение количества реплик 
Поработав с приложением, вы получили запрос на увеличение количества реплик приложения для нагрузки. Необходимо изменить запущенный deployment, увеличив количество реплик до 5. Посмотрите статус запущенных подов после увеличения реплик. 

Требования:
 * в deployment из задания 1 изменено количество реплик на 5
 * проверить что все поды перешли в статус running (kubectl get pods)

```commandline
Mikhails-MBP:12-02 mikhailrusakovich$ kubectl edit deployment hello-world
deployment.apps/hello-world edited
Mikhails-MBP:12-02 mikhailrusakovich$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
hello-world-75b9ff444f-46vrf   1/1     Running   0          3s
hello-world-75b9ff444f-595pl   1/1     Running   0          3s
hello-world-75b9ff444f-d2plg   1/1     Running   0          95s
hello-world-75b9ff444f-mknl9   1/1     Running   0          95s
hello-world-75b9ff444f-vbvzg   1/1     Running   0          3s

```

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
