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

- [Подготовка VMs](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/01-VM-provision.md)
- [Подготовка SSH и инструментов](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/02-SSH-Utils.md)
- [Создание CA и сертификатов](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/03-CA-Certs.md)
- [Создание kubeconfigs для компонентов](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/04-Kubeconfigs.md)
- [Создание ключа для шифрования secrets](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/05-Encrypt-at-Rest.md)
- [Установка etcd](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/06-ETCD.md)
- [Установка controlplane](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/07-Controlplane.md)
- [Установка worker](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/08-Workers.md)
- [Настройка kubectl](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/09-Kubectl-Access.md)
- [Настройка POD network](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/10-CNI-Plugin.md)
- [Настройка DNS solution](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/steps/11-CoreDNS.md)
- [Тесты](https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/blob/main/steps/12-Tests.md)
