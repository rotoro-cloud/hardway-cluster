# Предварительные требования

### VM Hardware

16 GB of RAM
50 GB Disk space

### Virtual Box

Скачай и установи [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
Поддерживаемые хостовые ОС:

 - Windows
 - OS X
 - Linux
 - Solaris

### Vagrant

Можно провижинить VMs вручную или с помощью Vagrant. 
#
#### **Вручную**

Мой вариант - Vagrant, но если очень хочется, то можно вручную. Для этого:

- Убедись, что VM [соответствует](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#verify-mac-address)
- Создай SSH ключи для каждой VM и проверь подключение к ним из своего терминала
- Отредактируй `/etc/hosts` на участниках кластера, чтобы они знали друг о друге
#
#### с Vagrant

Для него у меня подготовлен специальный скрипт, который развернет машины, подходящие под требования Kubernetes.

Скачать и установить [Vagrant](https://www.vagrantup.com/)

Поддерживаемые хостовые ОС:

- Windows
- Debian
- Centos
- Linux
- macOS

# Создание VMs

### Склонируй этот репо

В корне лежит `Vagrantfile`.

По умолчанию он настроен на создание 2 мастеров, 1 воркера и 1 лоадбаленсера.

```
git clone https://github.com/rotoro-cloud/hardway-cluster.git
cd hardway-cluster
vagrant up
```

Поднимутся VMs. Их адреса:
- 192.168.66.1X - для мастеров
- 192.168.66.2X - для рабочих
- 192.168.66.30 - для балансировщика

Т.е. `controlplane01` будет 192.168.66.11, `controlplane02` будет 192.168.66.12 и т.д.

Но сачала давай познакомимся с окружением

### Окружение
Ты можешь подключиться прямо к виртуальной машине с помощью
```
vagrant ssh node01
```
Или сделай команду:
```
vagrant ssh-config
```
Это покажет тебе адреса VMs, их порты ssh и пути к ключам, чтобы подключиться к ним из твоего любимого ssh-клиента.

### Балансировщик
Для сетапа высокой доступности необходимый элемент, без него не будет полноценной `HA`.
В этом сетапе мы используем `HAproxy`, давай ее настроим.

##### Выполняется в VM `lb`
```
sudo apt update
sudo apt install -y haproxy
```
Далее внесем правки в `/etc/haproxy/haproxy.cfg`, добавив в конец следующее:
```
frontend kubernetes-frontend
    bind 192.168.66.30:6443
    mode tcp
    option tcplog
    default_backend kubernetes-backend

backend kubernetes-backend
    mode tcp
    option tcp-check
    balance roundrobin
    server controlplane01 192.168.66.11:6443 check fall 3 rise 2
    server controlplane02 192.168.66.12:6443 check fall 3 rise 2
    server controlplane03 192.168.66.13:6443 check fall 3 rise 2
```
Теперь балансировщик будет пересылать трафик на один из трех мастеров. И он достаточно умный не послылать трафик, если мастер в данный момент недоступен. 
```
sudo systemctl restart haproxy
```
Мы закончили с балансировщиком, он нам больше не понадобится.


# Проверка

- Все машины работают
- Ты можешь подключиться по SSH к будущим нодам
- Все ноды и балансер пингуют друг друга

Следущий шаг: [Подготовка SSH и инструментов](https://github.com/rotoro-cloud/hardway-cluster/blob/main/steps/02-SSH-Utils.md)
