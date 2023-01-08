# Домашнее задание к занятию "12.4 Развертывание кластера на собственных серверах, лекция 2"
Новые проекты пошли стабильным потоком. Каждый проект требует себе несколько кластеров: под тесты и продуктив. Делать все руками — не вариант, поэтому стоит автоматизировать подготовку новых кластеров.

## Задание 1: Подготовить инвентарь kubespray
Новые тестовые кластеры требуют типичных простых настроек. Нужно подготовить инвентарь и проверить его работу. Требования к инвентарю:
* подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды;
* в качестве CRI — containerd;
* запуск etcd производить на мастере.

terraform.tfvars
```commandline
#Global Vars
aws_cluster_name = "k8snetology"

#VPC Vars
aws_vpc_cidr_block       = "10.250.192.0/18"
aws_cidr_subnets_private = ["10.250.192.0/20", "10.250.208.0/20"]
aws_cidr_subnets_public  = ["10.250.224.0/20", "10.250.240.0/20"]

# single AZ deployment
#aws_cidr_subnets_private = ["10.250.192.0/20"]
#aws_cidr_subnets_public  = ["10.250.224.0/20"]

# 3+ AZ deployment
#aws_cidr_subnets_private = ["10.250.192.0/24","10.250.193.0/24","10.250.194.0/24","10.250.195.0/24"]
#aws_cidr_subnets_public  = ["10.250.224.0/24","10.250.225.0/24","10.250.226.0/24","10.250.227.0/24"]

#Bastion Host
aws_bastion_num  = 1
aws_bastion_size = "t3.small"

#Kubernetes Cluster
aws_kube_master_num       = 1
aws_kube_master_size      = "t3.medium"
aws_kube_master_disk_size = 50

aws_etcd_num       = 1
aws_etcd_size      = "t3.medium"
aws_etcd_disk_size = 50

aws_kube_worker_num       = 4
aws_kube_worker_size      = "t3.medium"
aws_kube_worker_disk_size = 50

#Settings AWS ELB
aws_nlb_api_port    = 6443
k8s_secure_api_port = 6443

default_tags = {
  #  Env = "devtest"
  #  Product = "kubernetes"
}

inventory_file = "../../../inventory/hosts"

```

credentials.tfvar

```commandline
#AWS Access Key
AWS_ACCESS_KEY_ID = "AKIARUHNNKQQMPF73K4H"
#AWS Secret Key
AWS_SECRET_ACCESS_KEY = "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"
#EC2 SSH Key Name
AWS_SSH_KEY_NAME = "k8s"
#AWS Region
AWS_DEFAULT_REGION = "us-east-1"

```

hosts
```commandline
[all]
ip-10-250-205-105.ec2.internal ansible_host=10.250.205.105
ip-10-250-202-2.ec2.internal ansible_host=10.250.202.2
ip-10-250-221-64.ec2.internal ansible_host=10.250.221.64
ip-10-250-206-59.ec2.internal ansible_host=10.250.206.59
ip-10-250-215-74.ec2.internal ansible_host=10.250.215.74
ip-10-250-205-183.ec2.internal ansible_host=10.250.205.183
bastion ansible_host=54.146.5.59

[bastion]
bastion ansible_host=54.146.5.59

[kube_control_plane]
ip-10-250-205-105.ec2.internal

[kube_node]
ip-10-250-202-2.ec2.internal
ip-10-250-221-64.ec2.internal
ip-10-250-206-59.ec2.internal
ip-10-250-215-74.ec2.internal

[etcd]
ip-10-250-205-183.ec2.internal

[calico_rr]

[k8s_cluster:children]
kube_node
kube_control_plane
calico_rr

[k8s_cluster:vars]
apiserver_loadbalancer_domain_name="kubernetes-nlb-k8snetology-8c68fb6b4f43b5f3.elb.us-east-1.amazonaws.com"


```

## Задание 2 (*): подготовить и проверить инвентарь для кластера в AWS
Часть новых проектов хотят запускать на мощностях AWS. Требования похожи:
* разворачивать 5 нод: 1 мастер и 4 рабочие ноды;
* работать должны на минимально допустимых EC2 — t3.small.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
