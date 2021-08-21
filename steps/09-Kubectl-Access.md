# Настройка kubectl для удаленного доступа

Настроим `kubectl` для работы с использованием креденшиалс пользователя `admin`.

*Запускаем команды из той же директории, де создавали сертификаты для `admin`.

### Создание `admin kubeconfig`

Каждый `kubeconfig` требует `Kubernetes API Server` для подключения. Для поддержки `HA`, мы будем использовать IP нашего балансировщика, который переправит пакеты в бекенд одного из доступных мастеров.

Создадим файл `kubeconfig` для аутентификации как `admin`:

```
{
  KUBERNETES_LB_ADDRESS=192.168.66.30

  kubectl config set-cluster kubernetes-the-hard-way \
    --certificate-authority=ca.crt \
    --embed-certs=true \
    --server=https://${KUBERNETES_LB_ADDRESS}:6443

  kubectl config set-credentials admin \
    --client-certificate=admin.crt \
    --client-key=admin.key

  kubectl config set-context kubernetes-the-hard-way \
    --cluster=kubernetes-the-hard-way \
    --user=admin

  kubectl config use-context kubernetes-the-hard-way
}
```

Справка документации по [kubectl config](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)

# Проверка

Check the health of the remote Kubernetes cluster:

```
kubectl get componentstatuses
```

Получим:

```
Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE             ERROR
controller-manager   Healthy   ok
scheduler            Healthy   ok
etcd-0               Healthy   {"health":"true"}
etcd-1               Healthy   {"health":"true"}
```

List the nodes in the remote Kubernetes cluster:

```
kubectl get nodes
```

Получим:

```
NAME       STATUS      ROLES    AGE     VERSION
node01     NotReady    <none>   30s     v1.21.0
```

Следущий шаг: [Настройка POD network](10.md)
