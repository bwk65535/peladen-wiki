---
title: Instalasi Red Hat OpenShift Local (CodeReady Containers)
description: 
published: true
date: 2025-01-02T10:47:32.137Z
tags: labs, container-orchestration, openshift
editor: markdown
dateCreated: 2025-01-02T10:47:32.137Z
---

Red Hat Openshift Local atau sebelumnya disebut sebagai CodeReady Containers merupakan openshift cluster yang berjalan di single node. Openshift local digunakan untuk testing dan mengemulasi cluster openshift secara lokal. Openshift local sudah  menggunakan openshift versi 4, berbeda dengan minishift yang masih menggunakan versi 3.

Walaupun disebut sebagai "openshift versi lokal", tetapi tetap saja untuk menjalankan ini dibutuhkan spesifikasi Komputer/Laptop yang lumayan tinggi.

## Prasyarat
- Niat.
- Minimum Spesifikasi hardware:
    - 4 Virtual CPU (vCPUs)
    - 9GB RAM
    - 35GB Disk space

Untuk lab ini menggunakan spesifikasi seperti berikut:
- 16 vCPUs
- 32GB RAM
- 480 GB Disk

```
$ grep -c processor /proc/cpuinfo
16
$ grep MemTotal /proc/meminfo
MemTotal:       32773244 kB
```

## Step 1 - Instalasi Libvirt
Upgrade OS terlebih dahulu, lalu install libvirt.

```
$ sudo dnf upgrade -y && sudo dnf autoremove -y

$ sudo dnf install -y libvirt libvirt-libs libvirt-daemon-kvm qemu-kvm
$ sudo systemctl start libvirtd
$ sudo systemctl enable libvirtd
```

Masukan current user ke group libvirt & qemu.

`$ sudo usermod -aG libvirt,qemu ${USER}`

lalu exit & start new terminal.

## Step 2 - Download CRC dan persiapkan pull secret
Setelah mendownload CRC, ekstrak dan pindahkan ke `/usr/local/bin`.

```
$ curl -sSLO https://developers.redhat.com/content-gateway/rest/mirror/pub/openshift-v4/clients/crc/latest/crc-linux-amd64.tar.xz

$ tar xvf crc-linux-amd64.tar.xz
crc-linux-2.14.0-amd64/
crc-linux-2.14.0-amd64/LICENSE
crc-linux-2.14.0-amd64/crc

$ sudo cp crc-linux-*-amd64/crc /usr/local/bin
$ ls -l /usr/local/bin/
total 78716
-rwxr-xr-x. 1 root root 80601185 Feb 18 14:45 crc
$ sudo rm -rf crc-linux-*

$ crc version
CRC version: 2.14.0+1a1ef27
OpenShift version: 4.12.1
Podman version: 4.3.1
```

Selanjutnya kita perlu mengambil pull secret. Download pull secret dari link [https://console.redhat.com/openshift/create/local](https://console.redhat.com/openshift/create/local). Kita harus login dengan akun redhat yang dapat dibuat secara gratis. Lalu simpan ke file.
```
$ vi pull-secret.json
```

![download pull secret](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2023/03/download_pull_secret.png "download pull secret")

## Step 3 - Setup dan Install CRC
Sesuaikan value dengan spesifikasi server yang digunakan.

Setup CRC
```
$ crc config set cpus 8
$ crc config set memory 16384
$ crc config set disk-size 64

$ crc config set consent-telemetry no
$ crc config set kubeadmin-password P@ssW0rd
```

Start CRC
```
$ crc setup
$ crc start --pull-secret-file ./pull-secret.json
```

Jika instalasi sudah selesai, test login ke dalam cluster.
```
$ crc console –credentials
$ eval $(crc oc-env)
$ oc login -u kubeadmin -p P@ssW0rd https://api.crc.testing:6443

The server is accessible via web console at:
  https://console-openshift-console.apps-crc.testing

Log in as administrator:
  Username: kubeadmin
  Password: P@ssW0rd

Log in as user:
  Username: developer
  Password: developer

Use the 'oc' command line interface:
  $ eval $(crc oc-env)
  $ oc login -u developer https://api.crc.testing:6443

$ oc login -u kubeadmin -p P@ssW0rd https://api.crc.testing:6443
Login successful.

You have access to 66 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".

$ oc get nodes
NAME                 STATUS   ROLES                         AGE   VERSION
crc-rwwzd-master-0   Ready    control-plane,master,worker   12d   v1.25.4+a34b9e9
```