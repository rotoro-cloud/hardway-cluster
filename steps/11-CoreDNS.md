# Развертывание решения DNS в кластере

Здесь мы создадим [DNS service](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/), которая обеспечит `service discovery` в кластере. Отвечает за это DNS-сервер [CoreDNS](https://coredns.io/), который разворачивается непосредственно в кластере.

### Приложение DNS-сервера

Сервер разворачивается как `deployment` в кластере, для избыточности создаются 2 PODs:

```
kubectl apply -f https://raw.githubusercontent.com/rotoro-cloud/hardway-cluster/main/yaml/coredns.yaml
```

Получим:

```
serviceaccount/coredns created
clusterrole.rbac.authorization.k8s.io/system:coredns created
clusterrolebinding.rbac.authorization.k8s.io/system:coredns created
configmap/coredns created
deployment.apps/coredns created
service/kube-dns created
```

Список PODs, созданных `coredns`-deployment:

```
kubectl get pods -l k8s-app=kube-dns -n kube-system
```

Получим:

```
NAME                       READY   STATUS    RESTARTS   AGE
coredns-8494f9c688-h7l52   1/1     Running   1          4m40s
coredns-8494f9c688-jgrkf   1/1     Running   0          4m40s
```

Справка по [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/coredns/#installing-coredns)

# Проверка

Создадим `busybox`-deployment:

```
kubectl run busybox --image=busybox:1.28 --command -- sleep 1d
```

Сделаем `DNS lookup` для `kubernetes`-service из `busybox`-POD:

```
kubectl exec -it busybox -- nslookup kubernetes
```

Получим:

```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      kubernetes
Address 1: 10.96.0.1 kubernetes.default.svc.cluster.local
```
Следущий шаг: [Тесты](https://github.com/rotoro-cloud/hardway-cluster/blob/main/steps/12-Tests.md)
