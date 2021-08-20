Here is the plan to create a Kubernetes cluster with bare hands.
Uses Vagrant and Ubuntu.

# Deploy multi-masters HA cluster with `your hands`, `vagrant` and `ubuntu`.

Данный репо описывает шаги процесса установления и проверки кластера производственного уровня сложным путем без систем автоматического развертывания кластера.

### В курсе CKA мы выяснили, что самый простой сетап с высокой доступностью состоит из 6 элементов:

- балансировщика
- 3 мастер-нод
- 2 рабочих узлов

В силу ограниченности ресурсов я собираю локальный кластер из 2 мастеров, 1 рабочего и балансировщика.

### Мы устанавливаем кластер с непрерывным шифрованием между компонентами и RBAC авторизацией. Вот его компоненты:

- [kubernetes](https://github.com/kubernetes/kubernetes) v1.21.0
- [containerd](https://github.com/containerd/containerd) v1.4.4
- [coredns](https://github.com/coredns/coredns) v1.8.3
- [cni](https://github.com/containernetworking/cni) v0.9.1
- [etcd](https://github.com/etcd-io/etcd) v3.4.15

### Я разбил процесс на отдельные главы, которые отвечают за определенные функции:

- [Подготовка VMs](https://github.com/rotoro-cloud/hardway-cluster/blob/main/steps/01.md)
- [Подготовка SSH и инструментов](https://github.com/rotoro-cloud/hardway-cluster/steps/02.md)
- [Создание CA и сертификатов](https://github.com/rotoro-cloud/hardway-cluster/steps/03.md)
- [Создание kubeconfigs для компонентов](https://github.com/rotoro-cloud/hardway-cluster/steps/04.md)
- [Создание ключа для шифрования secrets](https://github.com/rotoro-cloud/hardway-cluster/steps/05.md)
- [Установка etcd](https://github.com/rotoro-cloud/hardway-cluster/steps/06.md)
- [Установка controlplane](https://github.com/rotoro-cloud/hardway-cluster/steps/07.md)
- [Установка worker](https://github.com/rotoro-cloud/hardway-cluster/steps/08.md)
- [Настройка kubectl](https://github.com/rotoro-cloud/hardway-cluster/steps/09.md)
- [Настройка POD network](https://github.com/rotoro-cloud/hardway-cluster/steps/10.md)
- [Настройка DNS solution](https://github.com/rotoro-cloud/hardway-cluster/steps/11.md)
- [Tests](https://github.com/rotoro-cloud/hardway-cluster/steps/12.md)
