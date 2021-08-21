# Smoke Test

Т.к. мы разворачивали наш сетап вручную, нам совершенно необходимо с разных сторон протестировать этот кластер.

### Шифрование данных

Проверим возможность шифрования `secrets` - [encrypt secret data at rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#verifying-that-data-is-encrypted).

Создадим `generic secret`:

```
kubectl create secret generic kubernetes-the-hard-way \
  --from-literal="mykey=mydata"
```

Посмотрим его `hexdump`, хранящийся в `etcd`:

```
sudo ETCDCTL_API=3 etcdctl get \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.crt \
  --cert=/etc/etcd/etcd-server.crt \
  --key=/etc/etcd/etcd-server.key\
  /registry/secrets/default/kubernetes-the-hard-way | hexdump -C
```

Получим:

```
00000000  2f 72 65 67 69 73 74 72  79 2f 73 65 63 72 65 74  |/registry/secret|
00000010  73 2f 64 65 66 61 75 6c  74 2f 6b 75 62 65 72 6e  |s/default/kubern|
00000020  65 74 65 73 2d 74 68 65  2d 68 61 72 64 2d 77 61  |etes-the-hard-wa|
00000030  79 0a 6b 38 73 3a 65 6e  63 3a 61 65 73 63 62 63  |y.k8s:enc:aescbc|
00000040  3a 76 31 3a 6b 65 79 31  3a a3 d9 6e e3 5f 03 26  |:v1:key1:..n._.&|
00000050  a8 26 02 17 0f e5 37 f9  62 0f 87 dc 89 72 b5 1d  |.&....7.b....r..|
00000060  7b 9b 12 3f 00 e7 94 90  8f d7 da 77 e2 0d c1 5a  |{..?.......w...Z|
00000070  3b 0f e1 10 87 e4 06 0c  24 93 51 be 6b ad f8 e5  |;.......$.Q.k...|
00000080  93 31 e1 54 b3 99 5b a5  56 3d f4 99 4a 70 e1 5e  |.1.T..[.V=..Jp.^|
00000090  af 3d 82 d1 80 18 3e cb  95 0b 1f 14 2e 82 52 5e  |.=....>.......R^|
000000a0  b2 0d 91 49 09 f2 46 26  d2 6a 3c cf d3 7a 67 62  |...I..F&.j<..zgb|
000000b0  af 44 c7 a7 39 de fc 5c  cb 48 e3 8c 97 0e dd d5  |.D..9..\.H......|
000000c0  7d 20 16 c7 d3 4a e5 07  28 eb 10 63 0d f1 a3 45  |} ...J..(..c...E|
000000d0  0c 6b 3c 4b 0b 7b 98 c4  d1 b3 a2 ed 31 3c e3 6d  |.k<K.{......1<.m|
000000e0  fc 84 0d fa 42 fa 51 b0  a6 11 69 fe da 44 63 4a  |....B.Q...i..DcJ|
000000f0  63 19 96 39 90 86 71 25  cb 35 0c 1c aa 5c ca 05  |c..9..q%.5...\..|
00000100  a2 9f 86 05 8a 91 99 d7  d9 91 5c 55 83 1d bf 02  |..........\U....|
00000110  fc 68 f0 87 9a da ec 10  07 06 6f 1e f8 f3 78 a9  |.h........o...x.|
00000120  4a eb 6e df f2 bc a8 96  72 18 9f b3 33 9f 60 6a  |J.n.....r...3.`j|
00000130  15 60 74 ef b7 94 75 8c  0e 76 00 93 b7 96 d3 c3  |.`t...u..v......|
00000140  de 97 9a a4 db 80 b5 8b  15 92 22 84 34 29 be ff  |..........".4)..|
00000150  67 f7 ac 60 d2 39 75 4f  4f 0a                    |g..`.9uOO.|
```

Здесь etcd-ключ имеет префикс `k8s:enc:aescbc:v1:key1`, что значит, что провайдер `aescbc` был использован для шифровки данных с помощью ключа `key1`.

### Deployments

Проверим возможность создания и управления [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

Развернем приложение [nginx](https://nginx.org/en/) вебсервер:

```
kubectl create deployment nginx --image=nginx
```

Посмотрим на PODs `nginx`-deployment:

```
kubectl get pods -l app=nginx
```

Получим:

```
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-9tvr5   1/1     Running   0          4m15s
```

### Перенаправление портов

Проверим возможность доступа к приложению удаленно с помощью [port forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/).

Получим полное имя `nginx`-POD:

```
POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath="{.items[0].metadata.name}")
```

Перенаправим порт `8080` своей локальной машины в порт `80` POD `nginx`:

```
kubectl port-forward $POD_NAME 8080:80
```

Получим:

```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

В новом терминале сделаем HTTP-запрос на сфорваженый адрес:

```
curl --head http://127.0.0.1:8080
```

Получим:

```
HTTP/1.1 200 OK
Server: nginx/1.21.1
Date: Fri, 20 Aug 2021 19:04:32 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 06 Jul 2021 14:59:17 GMT
Connection: keep-alive
ETag: "60e46fc5-264"
Accept-Ranges: bytes
```

Пререключимся в старый терминал и остановим `port forwarding` в POD `nginx`:

```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
Handling connection for 8080
^C
```

### Логи

Теперь проверим, может ли Kubernetes получать логи контейнеров - [retrieve container logs](https://kubernetes.io/docs/concepts/cluster-administration/logging/).

Посмотрим логи `nginx`-POD:

```
kubectl logs $POD_NAME
```

Получим:

```
...
127.0.0.1 - - [20/Aug/2021:19:04:32 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.58.0" "-"
```

### Exec

Проверим исполнение команд в контейнере - [execute commands in a container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/#running-individual-commands-in-a-container).

Выведем верию nginx командой `nginx -v` в контейнере `nginx`:

```
kubectl exec -ti $POD_NAME -- nginx -v
```

Получим:

```
nginx version: nginx/1.21.1
```

## Services

Проверим возможность выставлять приложения, создавая [service](https://kubernetes.io/docs/concepts/services-networking/service/).

Выставим `nginx`-deployment как [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport):

```
kubectl expose deployment nginx --port 80 --type NodePort
```

*Наш балансировшик работает для обеспечения `HA` кластера, но не балансирует рабочие нагрузки, поэтому эта служба будет доступна только на worker nodes*

Получим `node port` назначеннный `nginx`-service:

```
NODE_PORT=$(kubectl get svc nginx \
  --output=jsonpath='{range .spec.ports[0]}{.nodePort}')
```

Посмотрим страницу:

```
curl http://node01:$NODE_PORT
```

Получим:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
....
```

# Run End-to-End Tests

Install Go

```
wget https://dl.google.com/go/go1.15.linux-amd64.tar.gz

sudo tar -C /usr/local -xzf go1.15.linux-amd64.tar.gz
export GOPATH="/home/vagrant/go"
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
```

### Install kubetest

```
git clone https://github.com/kubernetes/test-infra.git
cd test-infra/
GO111MODULE=on go install ./kubetest
```

*Внимание, это большой репозиторий и скачивание занимает несколько минут*

### Настроим версию для нашего кластера и запустим тесты

```
K8S_VERSION=$(kubectl version -o json | jq -r '.serverVersion.gitVersion')
export KUBERNETES_CONFORMANCE_TEST=y
export KUBECONFIG="$HOME/.kube/config"

kubetest --provider=skeleton --test --test_args=”--ginkgo.focus=\[Conformance\]” --extract ${K8S_VERSION} | tee test.out
```

Это может занять от 1.5 до 2 часов. Количество тестов и результаты будет видно в конце теста.
