# SSH + утилиты

### Инструменты

Для начала надо определиться, откуда будет админиться кластер. В моем случае я собираюсь делать это с `controlplane01`.
На нем Ubuntu 18.04. В ней изначально есть практически все необходимое для наших операций. Это утилиты работы с `ssh`, `openssl`.
Однако `kubectl` придется установить.

### Ключи
Создим Key Pair на `controlplain01` с помощью `ssh-keygen`

Все оставим по умолчанию.

Просмотрим его ID

```
cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD......Tj5qkAVT7 vagrant@controlplane01
```

Теперь раскопируем этот публичный ключ на другие VMs

```
cat >> ~/.ssh/authorized_keys <<EOF
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD......Tj5qkAVT7 vagrant@controlplane01
EOF
```

### Ставим `kubectl`
#
#### OS X

```
curl -o kubectl https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/darwin/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```
#
#### Linux

```
wget https://storage.googleapis.com/kubernetes-release/release/v1.21.0/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

# Проверка

- С первого мастера можно SSH на другие ноды
- Версия `kubectl` 1.21.0 или выше:

```
kubectl version --client
```

Следущий шаг: [Создание CA и сертификатов](https://github.com/rotoro-cloud/hardway-cluster/blob/main/steps/03-CA-Certs.md)
