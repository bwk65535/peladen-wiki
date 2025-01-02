---
title: Instalasi k3s di Virtualbox dengan Calico CNI
description: 
published: true
date: 2025-01-02T10:43:57.717Z
tags: labs, container-orchestration, k3s
editor: markdown
dateCreated: 2025-01-02T10:43:57.717Z
---

K3s adalah salah satu distro kubernetes yang ditujukan untuk environment development, IoT, maupun untuk embedded devices karena penggunaan resourcenya yang lebih kecil. k3s di package dalam satu binary yang dapat di download sehingga dapat didistribusikan dan di deploy dengan mudah.

## Prasyarat
- Niat.
- 4 mesin yang sudah terinstall OS. Di lab ini akan menggunakan AlmaLinux 8.
  - 1 mesin sebagai k3s control plane (master).
  - 3 mesin sebagai worker node.
- SELinux dalam kondisi mati di semua server (biar ngga ribet aja wkwk).

Berikut setup lab yang digunakan:

| hostname | ip address | notes |
|---|---|---|
| master01 | 192.168.56.151/24 | Control plane 1 |
| worker01 | 192.168.56.152/24 | Worker node 1 |
| worker02 | 192.168.56.153/24 | Worker node 2 |
| worker03 | 192.168.56.154/24 | Worker node 3 |

Note: jika mau ngelab menggunakan virtualbox+vagrant, bisa menggunakan contoh [Vagrantfile](https://gist.github.com/bwk65535/4697a76dc256f4a5a3582b8430bed41e) berikut.

## Step 1 - OS Preparation
Matikan firewalld dan SELinux di semua server
```
$ sudo systemctl stop firewalld
$ sudo systemctl disable firewalld

$ sudo vi /etc/selinux/config
ubah "SELINUX=enforcing" menjadi "SELINUX=disabled"
```

Enable ipv4 forwarding
```
$ sudo vi /etc/sysctl.conf
# tambahkan baris berikut
net.ipv4.ip_forward=1

$ sudo sysctl -p
```

Reboot server

`sudo systemctl reboot`

## Step 2 - Instalasi Master node
Di lab ini kita akan menggunakan Calico sebagai network pluginnya, sehingga instalasi k3s dilakukan dengan men-disable flannel. Selain itu, pilih network address yang akan dipakai oleh pod network. Di contoh ini menggunakan network address bawaan k3s yaitu 10.42.0.0/16.

Note: karena di lab ini tiap servernya mempunyai lebih dari 1 interface, maka sebaiknya kita set ip address mana yang akan digunakan untuk komunikasi node di dalam cluster menggunakan options `--node-ip` dan `--advertise-address`.

Note 2: di lab ini semua command akan di eksekusi menggunakan user root supaya tidak perlu pusing issue permission.

```
* masuk ke user root
$ sudo su -
# curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--flannel-backend=none --node-ip=192.168.56.151 --advertise-address=192.168.56.151 --cluster-cidr=10.42.0.0/16 --write-kubeconfig-mode=644" sh -s -
```

Setelah selesai, file kube config akan ada di path `/etc/rancher/k3s/k3s.yaml`. Ketika kita menjalankan kubectl akan otomatis menggunakan kube config tersebut. Kita juga bisa mengkopinya ke mesin lain yang akan digunakan untuk me-remote cluster.

Pod-pod yang ada di master statusnya akan "Pending" karena kita belum menginstall network plugin di clusternya.
```
# kubectl get pods -A
NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE
kube-system   helm-install-traefik-jc942                0/1     Pending   0          36m
kube-system   metrics-server-668d979685-cqpgh           0/1     Pending   0          36m
kube-system   local-path-provisioner-7b7dc8d6f5-xtmc9   0/1     Pending   0          36m
kube-system   coredns-b96499967-khxqp                   0/1     Pending   0          36m
kube-system   helm-install-traefik-crd-bf4rk            0/1     Pending   0          36m
```

Berikan taint pada node master supaya tidak ada deployment apps yang masuk ke node master nantinya.
```
# kubectl taint nodes master01 node-role.kubernetes.io/master=true:NoSchedule
```

## Step 3 - Instalasi Calico CNI
Kita akan menginstall Calico menggunakan calico operator. Ada 2 bagian yang perlu diinstal, yaitu Calico operator beserta CRDs dan Caliconya itu sendiri.

Untuk instalasi calico operator dan CRDs tidak perlu ada yang dirubah, sehingga bisa langsung kita deploy saja.
```
# kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.24.3/manifests/tigera-operator.yaml

namespace/tigera-operator created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/caliconodestatuses.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipreservations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/kubecontrollersconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/apiservers.operator.tigera.io created
customresourcedefinition.apiextensions.k8s.io/imagesets.operator.tigera.io created
customresourcedefinition.apiextensions.k8s.io/installations.operator.tigera.io created
customresourcedefinition.apiextensions.k8s.io/tigerastatuses.operator.tigera.io created
serviceaccount/tigera-operator created
clusterrole.rbac.authorization.k8s.io/tigera-operator created
clusterrolebinding.rbac.authorization.k8s.io/tigera-operator created
deployment.apps/tigera-operator created
```

Sedangkan untuk instalasi calico perlu ada penyesuaian konfigurasi calico di dalam manifestnya, sehingga lebih baik kita download dulu lalu di modifikasi.
```
# curl -LO https://raw.githubusercontent.com/projectcalico/calico/v3.24.3/manifests/custom-resources.yaml
# vi custom-resources.yaml

* Tambahkan konfigurasi "containerIPForwarding: Enabled", 
sesuaikan block size dan pod cidr yang digunakan.
* Seperti note sebelumnya, karena di server2 lab ini ada lebih dari satu interface, 
maka set parameter "nodeAddressAutodetectionV4" sesuai subnet node supaya tidak salah mendeteksi ip node.

apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  calicoNetwork:
    containerIPForwarding: Enabled
    ipPools:
    - blockSize: 24
      cidr: 10.42.0.0/16
      encapsulation: VXLANCrossSubnet
      natOutgoing: Enabled
      nodeSelector: all()
    nodeAddressAutodetectionV4:
      cidrs:
        - 192.168.56.0/24

---

apiVersion: operator.tigera.io/v1
kind: APIServer
metadata:
  name: default
spec: {}
```

Lalu deploy file manifest tersebut.
```
$ kubectl apply -f custom-resources.yaml

installation.operator.tigera.io/default created
apiserver.operator.tigera.io/default created
```

Tunggu hingga status caliconya Available dan semua podnya Running.
```
$ kubectl get tigerastatus/calico
NAME     AVAILABLE   PROGRESSING   DEGRADED   SINCE
calico   True        False         False      7m35s

$ kubectl get pods -A -owide
NAMESPACE          NAME                                       READY   STATUS      RESTARTS        AGE
kube-system        helm-install-traefik-crd-bv55z             0/1     Completed   2               121m
kube-system        helm-install-traefik-npjxv                 0/1     Completed   4               121m
kube-system        local-path-provisioner-7b7dc8d6f5-8z849    1/1     Running     0               121m
calico-system      csi-node-driver-gmggs                      2/2     Running     0               31m
calico-system      calico-kube-controllers-85b7ffdbf5-68ffv   1/1     Running     3 (10m ago)     33m
calico-system      calico-typha-79797768d9-b7gdm              1/1     Running     2 (12m ago)     33m
tigera-operator    tigera-operator-5569c6548f-nzq4j           1/1     Running     5 (11m ago)     112m
kube-system        svclb-traefik-5bf88b59-jsqzk               2/2     Running     0               22m
calico-system      calico-node-bqv52                          1/1     Running     5 (9m ago)      33m
kube-system        traefik-7cd4fcff68-9mhh9                   1/1     Running     3 (9m59s ago)   23m
calico-apiserver   calico-apiserver-76bc5445d4-dk76m          1/1     Running     6 (6m55s ago)   28m
calico-apiserver   calico-apiserver-76bc5445d4-nqwh7          1/1     Running     6 (6m40s ago)   28m
kube-system        metrics-server-668d979685-kxmsz            1/1     Running     6 (9m58s ago)   121m
kube-system        coredns-b96499967-8qqg6                    1/1     Running     6 (9m34s ago)   121m
```

## Step 4 - Instalasi Worker Node
Untuk menginstal dan menambahkan worker node ke dalam cluster, kita memerlukan informasi **K3S_URL** dan **K3S_TOKEN**. K3S_URL merupakan alamat endpont dari api server dalam hal ini ip address master dengan port 6443. Untuk K3S_TOKEN dapat kita peroleh dari node master di path `/var/lib/rancher/k3s/server/node-token`. Jangan lupa atur juga ip untuk parameter `--node-ip` menggunakan ip address worker node tersebut.

```
* cek di master node
# cat /var/lib/rancher/k3s/server/node-token
K10da6f8a47c415b7b791b7c5e65b0b7fc893d88e8901499fd4087ca97ee0494b24::server:86c4637d322844fc8cf9e5e0c8d7a43d
```

```
* jalankan di worker node 1
$ sudo su -
# curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.151:6443 K3S_TOKEN=K10da6f8a47c415b7b791b7c5e65b0b7fc893d88e8901499fd4087ca97ee0494b24::server:86c4637d322844fc8cf9e5e0c8d7a43d INSTALL_K3S_EXEC="--node-ip=192.168.56.152" sh -s -
```

Cek apakah status worker node ready dan semua pod di node tersebut sudah Running.
```
# kubectl get nodes -owide
NAME       STATUS   ROLES                  AGE     VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION                 CONTAINER-RUNTIME
master01   Ready    control-plane,master   13h     v1.24.6+k3s1   192.168.56.151   <none>        AlmaLinux 8.6 (Sky Tiger)   4.18.0-372.26.1.el8_6.x86_64   containerd://1.6.8-k3s1
worker01   Ready    <none>                 5m24s   v1.24.6+k3s1   192.168.56.152   <none>        AlmaLinux 8.6 (Sky Tiger)   4.18.0-372.26.1.el8_6.x86_64   containerd://1.6.8-k3s1

# kubectl get pods -A -owide | grep worker01
calico-system      calico-node-46qg6                          1/1     Running             0               6m5s    192.168.56.152   worker01   <none>           <none>
kube-system        svclb-traefik-58b0da44-dpxsf               2/2     Running             0               4m13s   10.42.20.1       worker01   <none>           <none>
calico-system      csi-node-driver-55hxv                      2/2     Running             0               4m13s   10.42.20.2       worker01   <none>           <none>
```

Akan kita lakukan hal yang sama di 2 worker node lainnya, sehingga total akan ada 3 worker node di dalam cluster.
```
* jalankan di worker 2
# curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.151:6443 K3S_TOKEN=K10da6f8a47c415b7b791b7c5e65b0b7fc893d88e8901499fd4087ca97ee0494b24::server:86c4637d322844fc8cf9e5e0c8d7a43d INSTALL_K3S_EXEC="--node-ip=192.168.56.153" sh -s -

* jalankan di worker 3
# curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.151:6443 K3S_TOKEN=K10da6f8a47c415b7b791b7c5e65b0b7fc893d88e8901499fd4087ca97ee0494b24::server:86c4637d322844fc8cf9e5e0c8d7a43d INSTALL_K3S_EXEC="--node-ip=192.168.56.154" sh -s -
```

Cek kembali node yang sudah join dan pod-pod yang berjalan di dalamnya.
```
# kubectl get nodes -owide
NAME       STATUS   ROLES                  AGE     VERSION        INTERNAL-IP      EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION                 CONTAINER-RUNTIME
worker02   Ready    <none>                 5m25s   v1.24.6+k3s1   192.168.56.153   <none>        AlmaLinux 8.6 (Sky Tiger)   4.18.0-372.26.1.el8_6.x86_64   containerd://1.6.8-k3s1
worker03   Ready    <none>                 5m6s    v1.24.6+k3s1   192.168.56.154   <none>        AlmaLinux 8.6 (Sky Tiger)   4.18.0-372.26.1.el8_6.x86_64   containerd://1.6.8-k3s1
worker01   Ready    <none>                 11m     v1.24.6+k3s1   192.168.56.152   <none>        AlmaLinux 8.6 (Sky Tiger)   4.18.0-372.26.1.el8_6.x86_64   containerd://1.6.8-k3s1
master01   Ready    control-plane,master   13h     v1.24.6+k3s1   192.168.56.151   <none>        AlmaLinux 8.6 (Sky Tiger)   4.18.0-372.26.1.el8_6.x86_64   containerd://1.6.8-k3s1

# kubectl get pods -A -owide
NAMESPACE          NAME                                       READY   STATUS      RESTARTS      AGE     IP               NODE       NOMINATED NODE   READINESS GATES
calico-system      calico-typha-5ff6f445b4-p7j6l              1/1     Running     0             149m    192.168.56.151   master01   <none>           <none>
calico-system      calico-node-pphl4                          1/1     Running     0             149m    192.168.56.151   master01   <none>           <none>
kube-system        local-path-provisioner-7b7dc8d6f5-jvhcr    1/1     Running     0             13h     10.42.170.1      master01   <none>           <none>
kube-system        coredns-b96499967-gzmtg                    1/1     Running     0             13h     10.42.170.5      master01   <none>           <none>
calico-system      csi-node-driver-fl7ms                      2/2     Running     0             148m    10.42.170.4      master01   <none>           <none>
calico-system      calico-kube-controllers-85b7ffdbf5-zdk5l   1/1     Running     0             149m    10.42.170.6      master01   <none>           <none>
kube-system        helm-install-traefik-crd-lc9fk             0/1     Completed   0             13h     10.42.170.3      master01   <none>           <none>
kube-system        helm-install-traefik-k48jl                 0/1     Completed   2             13h     10.42.170.7      master01   <none>           <none>
kube-system        svclb-traefik-58b0da44-4zdnv               2/2     Running     0             144m    10.42.170.11     master01   <none>           <none>
kube-system        metrics-server-668d979685-8kcf2            1/1     Running     0             13h     10.42.170.2      master01   <none>           <none>
kube-system        traefik-7cd4fcff68-rdljt                   1/1     Running     0             144m    10.42.170.10     master01   <none>           <none>
tigera-operator    tigera-operator-5569c6548f-p8k7b           1/1     Running     2 (10m ago)   13h     192.168.56.151   master01   <none>           <none>
calico-apiserver   calico-apiserver-8477647967-d4ffb          1/1     Running     1 (10m ago)   144m    10.42.170.9      master01   <none>           <none>
calico-apiserver   calico-apiserver-8477647967-psb9c          1/1     Running     1 (10m ago)   144m    10.42.170.8      master01   <none>           <none>
calico-system      calico-node-46qg6                          1/1     Running     0             11m     192.168.56.152   worker01   <none>           <none>
kube-system        svclb-traefik-58b0da44-dpxsf               2/2     Running     0             9m33s   10.42.20.1       worker01   <none>           <none>
calico-system      csi-node-driver-55hxv                      2/2     Running     0             9m33s   10.42.20.2       worker01   <none>           <none>
calico-system      calico-typha-5ff6f445b4-8vmwg              1/1     Running     0             5m42s   192.168.56.153   worker02   <none>           <none>
calico-system      calico-node-pgv57                          1/1     Running     0             5m50s   192.168.56.153   worker02   <none>           <none>
kube-system        svclb-traefik-58b0da44-pjrmk               2/2     Running     0             3m45s   10.42.121.1      worker02   <none>           <none>
calico-system      calico-node-9zl56                          1/1     Running     0             5m32s   192.168.56.154   worker03   <none>           <none>
calico-system      csi-node-driver-5ssdd                      2/2     Running     0             3m45s   10.42.121.2      worker02   <none>           <none>
kube-system        svclb-traefik-58b0da44-lxbrj               2/2     Running     0             3m19s   10.42.22.2       worker03   <none>           <none>
calico-system      csi-node-driver-m8mtg                      2/2     Running     0             3m19s   10.42.22.1       worker03   <none>           <none>
```

## Step 5 - Test deployment
Untuk pengetesan, kita akan mendeploy nginx image ke cluster menggunakan contoh manifest berikut:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-nginx
  labels:
    app: test-nginx
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: test-nginx
  template:
    metadata:
      labels:
        app: test-nginx
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - test-nginx
            topologyKey: "kubernetes.io/hostname"
      containers:
        - name: nginx
          image: nginx:stable-alpine
          ports:
          - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: test-nginx
  name: test-nginx
  namespace: default
spec:
  ports:
  - nodePort: 30080
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: test-nginx
  type: NodePort
```

Contoh deployment ini menggunakan `podAntiAffinity` supaya 3 pod replikanya tersebar di node yang berbeda. Supaya memudahkan pengetesan, kita juga mengexpose ketiga pod tersebut menggunakan service type `NodePort`. Pod test-nginx akan dapat diakses di semua alamat node dengan port 30080.

```
# kubectl apply -f test-nginx.yaml
deployment.apps/test-nginx created

# kubectl get pods -owide
NAME                          READY   STATUS    RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES
test-nginx-79448d497f-hxjf6   1/1     Running   0          7m51s   10.42.22.4    worker03   <none>           <none>
test-nginx-79448d497f-4tpbl   1/1     Running   0          7m50s   10.42.20.4    worker01   <none>           <none>
test-nginx-79448d497f-xdl8g   1/1     Running   0          7m50s   10.42.121.4   worker02   <none>           <none>

# kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.43.0.1      <none>        443/TCP        13h
test-nginx   NodePort    10.43.185.58   <none>        80:30080/TCP   8m1s
```

Test curl ke salah satu node
```
# curl --head http://192.168.56.152:30080
HTTP/1.1 200 OK
Server: nginx/1.22.1
Date: Tue, 25 Oct 2022 03:51:35 GMT
Content-Type: text/html
Content-Length: 615
Last-Modified: Wed, 19 Oct 2022 10:49:37 GMT
Connection: keep-alive
ETag: "634fd641-267"
Accept-Ranges: bytes
```