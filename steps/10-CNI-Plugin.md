# Установка POD network

Как и в курсе CKA, здесь мы используем [CNI-weave](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/).

### Установка плагинов

Мы скачали и установили `CNI`, когда бутстрапили рабочие ноды.

Справка по [CNI](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#cni)

### Развертывание Weave Network

Это требуется выполнить один раз на мастере.


```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

*Weave использует POD CIDR `10.32.0.0/12` по умолчанию*

# Проверка

Сетевой плагин развернулся как `daemonset` на рабочих нодах кластера:

```
kubectl get pods -n kube-system
```

Получим

```
NAME              READY   STATUS    RESTARTS   AGE
weave-net-lwhkz   2/2     Running   1          49s

```

Ссылки по использованию [weave-net-addon](https://kubernetes.io/docs/tasks/administer-cluster/network-policy-provider/weave-network-policy/#install-the-weave-net-addon)

Следущий шаг: [Настройка DNS solution](https://github.com/rotoro-cloud/hardway-cluster/blob/main/steps/11-CoreDNS.md)
