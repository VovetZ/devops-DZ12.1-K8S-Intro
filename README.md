### Чеклист готовности к домашнему заданию

1. Личный компьютер с ОС Linux или MacOS

или

2. ВМ c ОС Linux в облаке либо ВМ на локальной машине для установки MicroK8S 

------

### Инструкция к заданию

1. Установка MicroK8S:
    - sudo apt update,
    - sudo apt install snapd,
    - sudo snap install microk8s --classic,
    - добавить локального пользователя в группу `sudo usermod -a -G microk8s $USER`,
    - изменить права на папку с конфигурацией `sudo chown -f -R $USER ~/.kube`.

2. Полезные команды:
    - проверить статус `microk8s status --wait-ready`;
    - подключиться к microK8s и получить информацию можно через команду `microk8s command`, например, `microk8s kubectl get nodes`;
    - включить addon можно через команду `microk8s enable`;
    - список addon `microk8s status`;
    - вывод конфигурации `microk8s config`;
    - проброс порта для подключения локально `microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443`.


3. Настройка внешнего подключения:

    - отредактировать файл /var/snap/microk8s/current/certs/csr.conf.template

    ```shell
    # [ alt_names ]
    # Add
    # IP.4 = 123.45.67.89
    ```

    - обновить сертификаты `sudo microk8s refresh-certs --cert front-proxy-client.crt`.

4. Установка kubectl:

    - curl -LO <https://storage.googleapis.com/kubernetes-release/release/>`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl;
    - chmod +x ./kubectl;
    - sudo mv ./kubectl /usr/local/bin/kubectl;
    - настройка автодополнения в текущую сессию `bash source <(kubectl completion bash)`;
    - добавление автодополнения в командную оболочку bash `echo "source <(kubectl completion bash)" >> ~/.bashrc`.
------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Инструкция](https://microk8s.io/docs/getting-started) по установке MicroK8S.
2. [Инструкция](https://kubernetes.io/ru/docs/reference/kubectl/cheatsheet/#bash) по установке автодополнения **kubectl**.
3. [Шпаргалка](https://kubernetes.io/ru/docs/reference/kubectl/cheatsheet/) по **kubectl**.
------

### Задание 1. Установка MicroK8S
1. Установить MicroK8S на локальную машину или на удалённую виртуальную машину.
2. Установить dashboard.
3. Сгенерировать сертификат для подключения к внешнему ip-адресу.

------

### Задание 2. Установка и настройка локального kubectl
 
1. Установить на локальную машину kubectl.
2. Настроить локально подключение к кластеру.
3. Подключиться к дашборду с помощью port-forward.

------

## Ответ

#### Установка MicroK8S

```shell
root@vkvm:/home/vk# apt update
Hit:1 http://ru.archive.ubuntu.com/ubuntu jammy InRelease
Hit:2 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease         
Hit:3 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease       
Hit:4 https://download.docker.com/linux/ubuntu jammy InRelease            
Hit:5 http://security.ubuntu.com/ubuntu jammy-security InRelease          
Reading package lists... Done                        
Building dependency tree... Done
Reading state information... Done
10 packages can be upgraded. Run 'apt list --upgradable' to see them.
root@vkvm:/home/vk# apt upgrade
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
Get more security updates through Ubuntu Pro with 'esm-apps' enabled:
  libimage-magick-perl imagemagick libopenexr25 libmagick++-6.q16-8
  libpostproc55 libmagickcore-6.q16-6-extra libavcodec58
  libimage-magick-q16-perl libmagickwand-6.q16-6 libavutil56 imagemagick-6.q16
  libswscale5 libmagickcore-6.q16-6 libswresample3 imagemagick-6-common
  libavformat58 libavfilter7
Learn more about Ubuntu Pro at https://ubuntu.com/pro
The following packages have been kept back:
  alsa-ucm-conf gnome-remote-desktop grub-efi-amd64-bin grub-efi-amd64-signed libpulse-mainloop-glib0
  libpulse0 libpulsedsp pulseaudio pulseaudio-module-bluetooth pulseaudio-utils
0 upgraded, 0 newly installed, 0 to remove and 10 not upgraded.
root@vkvm:/home/vk# apt install snapd
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
snapd is already the newest version (2.58+22.04).
snapd set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 10 not upgraded.
root@vkvm:/home/vk# snap install microk8s --classic
microk8s (1.26/stable) v1.26.3 from Canonical✓ installed
root@vkvm:/home/vk# usermod -a -G microk8s $USER
root@vkvm:/home/vk# chown -f -R $USER ~/.kube
```

#### Проверка статуса MicroK8S

```shell
root@vkvm:/home/vk# microk8s status --wait-ready
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # (core) Configure high availability on the current node
    helm                 # (core) Helm - the package manager for Kubernetes
    helm3                # (core) Helm 3 - the package manager for Kubernetes
  disabled:
    cert-manager         # (core) Cloud native certificate management
    community            # (core) The community addons repository
    dashboard            # (core) The Kubernetes dashboard
    dns                  # (core) CoreDNS
    gpu                  # (core) Automatic enablement of Nvidia CUDA
    host-access          # (core) Allow Pods connecting to Host services smoothly
    hostpath-storage     # (core) Storage class; allocates storage from host directory
    ingress              # (core) Ingress controller for external access
    kube-ovn             # (core) An advanced network fabric for Kubernetes
    mayastor             # (core) OpenEBS MayaStor
    metallb              # (core) Loadbalancer for your Kubernetes cluster
    metrics-server       # (core) K8s Metrics Server for API access to service metrics
    minio                # (core) MinIO object storage
    observability        # (core) A lightweight observability stack for logs, traces and metrics
    prometheus           # (core) Prometheus operator for monitoring and logging
    rbac                 # (core) Role-Based Access Control for authorisation
    registry             # (core) Private image registry exposed on localhost:32000
    storage              # (core) Alias to hostpath-storage add-on, deprecated
```

#### Подключение к K8S с выводом списка нод

```shell
root@vkvm:/home/vk# microk8s kubectl get nodes
NAME   STATUS   ROLES    AGE     VERSION
vkvm   Ready    <none>   2m41s   v1.26.3
```

#### Вывод конфигурации кластера

```shell
root@vkvm:/home/vk# microk8s config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUREekNDQWZlZ0F3SUJBZ0lVYmJTKzl5aE84ZFJqYU96ZFhLVHR1UVZhMmpNd0RRWUpLb1pJaHZjTkFRRUwKQlFBd0Z6RVZNQk1HQTFVRUF3d01NVEF1TVRVeUxqRTRNeTR4TUI0WERUSXpNRFF3TmpFM01UQXhOVm9YRFRNegpNRFF3TXpFM01UQXhOVm93RnpFVk1CTUdBMVVFQXd3TU1UQXVNVFV5TGpFNE15NHhNSUlCSWpBTkJna3Foa2lHCjl3MEJBUUVGQUFPQ0FROEFNSUlCQ2dLQ0FRRUF4M24wcjVmekc2WXpINmxTL25VQmhsTjNOUU92bUp2QlgyYmkKSXE5SDdkOVlkdUVYRDZKYlZidG1Cdm1Ka3ZyU3lBL1l4NzNPYUF0LzRxQ2pvWGpvek12bGgwK1dIUmhsUVFMTgpJVEl0SFVQMTcwNkdyS1NTSVV5VWtCOFlhY0VBUS9VZ25HS3Q1UGQxZ1hPN24rV2FUdWFOVHJDbEc3bW1aYjVFCm9LblNTNUZqR2sxcG9ISzJpeEFERWl6WG1WVGxxc2lDbWU4TUxHNW56RXJjdkF0MXp5SXJyWjdEZzh4dGFubW8KUGJRWGJqWlU4WmJsSEdSMkVTQXEwaDA3MCtET0x3bWpzY0tac2NyeHIzeWxXNlNzZ0lhcDJBT1owbUFSL2hYawpLdVlTeGNtZW9vTy9tZVduc21EOFZ6ZHFNNEF1dnVSbVFrSFczVVJLakwyb3lHR1dVUUlEQVFBQm8xTXdVVEFkCkJnTlZIUTRFRmdRVTVGdnlFc3pSU3RzWVFORDNHOFV6VXhuV2NSRXdId1lEVlIwakJCZ3dGb0FVNUZ2eUVzelIKU3RzWVFORDNHOFV6VXhuV2NSRXdEd1lEVlIwVEFRSC9CQVV3QXdFQi96QU5CZ2txaGtpRzl3MEJBUXNGQUFPQwpBUUVBU0FVdS90VElmdU1rQzV0dkV1STQ0OW1IbExnMENkY0dJN0V3YVBVRDZGOW9UVFVNTkdzdWplSXVQdGtzCmxVMFIwNzE4LzcvMmJFKzRsaTNjamVlZnhNM0NTU3lKL3FBM3FUTXVzaUVBcUp5MEpvMTc2RmJKdU9ZZG9KTXUKT2NaVjh5QkEyR0tFU255QjF6SDNJMVhvVDY5L1NubUJnRjJGZnhUNWZ4RmhUSVNFNEZmTk5FWkJjdEM3Zmg0NwpQTHl0eW8xUEFGaGFmSmZrSGEzeHFVTy9qekFGOVd1UnZpUDdHaWlYRDY2bGU5Nzd2ZzlMMDF6VGVockQ1RWcyCklBR2xtVDBjOThZck14RjRsOGpCa0xvUnRHQ2RkSFk1OWg2STdwTnYydUdKUlNDS0l5dk1jZFJnZGh0MXU1MnUKOE00WmRyZWFtRDhPeUNnc3JXS3c3QzVZYWc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    server: https://192.168.121.135:16443
  name: microk8s-cluster
contexts:
- context:
    cluster: microk8s-cluster
    user: admin
  name: microk8s
current-context: microk8s
kind: Config
preferences: {}
users:
- name: admin
  user:
    token: OE....0K
```

#### Настройка Dashboard

```shell
root@vkvm:/home/vk# microk8s enable dashboard
Infer repository core for addon dashboard
Enabling Kubernetes Dashboard
Infer repository core for addon metrics-server
Enabling Metrics-Server
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
clusterrolebinding.rbac.authorization.k8s.io/microk8s-admin created
Metrics-Server is enabled
Applying manifest
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
secret/microk8s-dashboard-token created

If RBAC is not enabled access the dashboard using the token retrieved with:

microk8s kubectl describe secret -n kube-system microk8s-dashboard-token

Use this token in the https login UI of the kubernetes-dashboard service.

In an RBAC enabled setup (microk8s enable RBAC) you need to create a user with restricted
permissions as shown in:
https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

```

#### Получение токена для доступа к Dashboard

```shell
root@vkvm:/home/vk# microk8s kubectl describe secret -n kube-system
Name:         kubernetes-dashboard-certs
Namespace:    kube-system
Labels:       k8s-app=kubernetes-dashboard
Annotations:  <none>

Type:  Opaque

Data
====


Name:         microk8s-dashboard-token
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: d2c1fbfb-ad09-4e4a-8659-0b29476740c4

Type:  kubernetes.io/service-account-token

Data
====
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Inl2TUYwa0h6TDNKbzRSa1Znck5WVWNETkNkZTdQdkZlZkZfYzlaNkZMTVUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJtaWNyb2s4cy1kYXNoYm9hcmQtdG9rZW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImQyYzFmYmZiLWFkMDktNGU0YS04NjU5LTBiMjk0NzY3NDBjNCIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTpkZWZhdWx0In0.cruUb0hTUE02v1AGTy21jZGHoSGew9j-2aDYFa_OVjsKIMsPm1W7A94eO7lrIz58ThUJMXimwNvAF7TEeuLm0DCeGU22cHX9W1tiw5F78A7a4YVsX1U0LPOByAXdcKC6q5q7QYLvED2wl-a7wtSgTyhH25FRSfS54U869wPk-rAAFsrKxlcSmUpyGEakbqxGpbBhkANS1nwiteIfEKvn8RSKA5QMoM8CBEilUztk8NydSG60mPGyqpSn1EQvpq4u4P_YOXBDaoGiT0xDeavOMtVTfsfahwCTg_G6ThCUcebD9XDi08Tz7tC_kI-kwYpTGZUoxpG4XuT7L9mmXEFppQ
ca.crt:     1123 bytes


Name:         kubernetes-dashboard-csrf
Namespace:    kube-system
Labels:       k8s-app=kubernetes-dashboard
Annotations:  <none>

Type:  Opaque

Data
====
csrf:  256 bytes


Name:         kubernetes-dashboard-key-holder
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
priv:  1679 bytes
pub:   459 bytes
``` 

#### Добавим IP адрес в запрос сертификата

```shell
root@vkvm:/home/vk# cat /var/snap/microk8s/current/certs/csr.conf.template | grep IP | grep -v "#"
IP.1 = 127.0.0.1
IP.2 = 10.152.183.1
IP.3 = 192.168.121.135
root@vkvm:/home/vk# microk8s refresh-certs --cert front-proxy-client.crt
Taking a backup of the current certificates under /var/snap/microk8s/4959/certs-backup/
Creating new certificates
Signature ok
subject=CN = front-proxy-client
Getting CA Private Key
Restarting service kubelite.
```

#### Установка kubectl

```shell
root@vkvm:/home/vk# apt-get install -y ca-certificates curl
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20211016ubuntu0.22.04.1).
curl is already the newest version (7.81.0-1ubuntu1.10).
0 upgraded, 0 newly installed, 0 to remove and 10 not upgraded.
root@vkvm:/home/vk# curl -fsSLo /etc/apt/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
root@vkvm:/home/vk# echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list
deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main
root@vkvm:/home/vk# apt update
Hit:1 http://ru.archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]                                    
Get:3 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease [108 kB]                                  
Hit:4 http://security.ubuntu.com/ubuntu jammy-security InRelease                                              
Hit:5 https://download.docker.com/linux/ubuntu jammy InRelease                                         
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [8 993 B]     
Get:7 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main amd64 DEP-11 Metadata [101 kB]
Get:8 http://ru.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 DEP-11 Metadata [269 kB]
Get:9 http://ru.archive.ubuntu.com/ubuntu jammy-updates/multiverse amd64 DEP-11 Metadata [940 B]
Get:10 http://ru.archive.ubuntu.com/ubuntu jammy-backports/main amd64 DEP-11 Metadata [8 000 B]
Get:11 http://ru.archive.ubuntu.com/ubuntu jammy-backports/universe amd64 DEP-11 Metadata [12,5 kB]
Get:12 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 Packages [64,5 kB]
Fetched 692 kB in 3s (272 kB/s)       
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
10 packages can be upgraded. Run 'apt list --upgradable' to see them.
root@vkvm:/home/vk# apt install kubectl
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  kubectl
0 upgraded, 1 newly installed, 0 to remove and 10 not upgraded.
Need to get 10,1 MB of archives.
After this operation, 48,0 MB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.26.3-00 [10,1 MB]
Fetched 10,1 MB in 2s (5 030 kB/s)    
Selecting previously unselected package kubectl.
(Reading database ... 257581 files and directories currently installed.)
Preparing to unpack .../kubectl_1.26.3-00_amd64.deb ...
Unpacking kubectl (1.26.3-00) ...
Setting up kubectl (1.26.3-00) ...
```

 

#### Настройка автодополнения в bash

```shell
root@vkvm:/home/vk# apt install bash-completion
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
bash-completion is already the newest version (1:2.11-5ubuntu1).
bash-completion set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 10 not upgraded.
root@vkvm:/home/vk# echo 'source <(kubectl completion bash)' >>~/.bashrc
root@vkvm:/home/vk# source ~/.bashrc
```

####  ~/.kube/config для подключения к кластеру

```yml
    apiVersion: v1
    clusters:
    - cluster:
        server: https://192.168.121.135:16443
        insecure-skip-tls-verify: true
      name: microk8s-cluster
    contexts:
    - context:
        cluster: microk8s-cluster
        user: admin
      name: microk8s
    current-context: microk8s
    kind: Config
    preferences: {}
    users:
    - name: admin
      user:
        token: OEc4NDJqeGxrelZxRVZpT0M3SjRaeERYTjF1T3hVR1pFRFpPYTlDeENTND0K
```

#### Cписок нод

```bash
root@vkvm:/home/vk# kubectl get nodes
NAME   STATUS   ROLES    AGE   VERSION
vkvm   Ready    <none>   24m   v1.26.3
```

#### Проброс порта для локального подключения

```bash
root@vkvm:/home/vk# microk8s kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443  --address 192.168.121.135
Forwarding from 192.168.121.135:10443 -> 8443
Handling connection for 10443
```

#### Подключаемся к Dashboard 

Вводим сгенерированный токен для аутентификации `https://192.168.121.135:10443`
![ Dashboard ](2.png)
