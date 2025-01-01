---
title: Pengenalan Helm
description: 
published: true
date: 2025-01-01T15:06:43.407Z
tags: containers, helm
editor: markdown
dateCreated: 2025-01-01T15:06:43.407Z
---

Kubernetes merupakan container orchestration yang saat ini paling banyak digunakan. Dalam menggunakan kubernetes, ada banyak file manifest dalam format yaml yang harus kita manage. Untuk satu deployment app saja, bisa ada beberapa file yaml untuk objek-objek berikut:
- Deployment
- Secret
- Service
- ConfigMap
- PV/PVC

Walaupun file-file untuk objek tersebut dapat kita satukan menjadi satu file, tetapi bayangkan jika kita punya banyak apps yang harus dijalankan di dalam kubernetes. Akan sangat sulit me-manage file manifest untuk tiap apps, belum lagi jika kita ingin melakukan perubahan pada isi manifest.

## Apa itu Helm
Helm merupakan sebuah *package manager* yang dapat membantu kita untuk melakukan deployment di kubernetes. Kita tidak perlu lagi me-manage file-file manifest kubernetes secara manual. Kita hanya perlu mendeklarasikan objek-objek di kubernetes pada Helm values, lalu helm yang akan membuat dan mengaplikasikan kubernetes manifest yang dibuatnya.

Helm juga akan melakukan *tracking* setiap deployment yang dilakukan, sehingga akan memudahkan kita dalam melakukan instalasi app, upgrade, rollback, maupun uninstall. Dengan adanya Helm kita tidak lagi melihat app sebagai kumpulan dari beberapa yaml manifest, tetapi hanya sebagai package.

## Instalasi Helm
Kita bisa menginstall Helm via script, package manager, atau download binary releasenya manual. Berikut contoh penginstalan menggunakan script:

~~~
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
Downloading https://get.helm.sh/helm-v3.8.2-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm

$ helm version
version.BuildInfo{Version:"v3.8.2", GitCommit:"6e3701edea09e5d55a8ca2aae03a68917630e91b", GitTreeState:"clean", GoVersion:"go1.17.5"}
~~~

## Helm2 vs Helm3
Ada 2 perbedaan mendasar yang cukup signifikan antara Helm2 yang di-release sekitar tahun 2016, dan Helm3 yang di-release akhir tahun 2019.

- Helm Tiller
Pada Helm2 ada komponen yang harus dijalankan di sisi kubernetes cluster, yaitu Tiller. Helm client/cli akan berkomunikasi dengan Tiller untuk melakukan perubahan di cluster. Karena kebutuhan ini, Tiller harus dijalankan dengan privilege yang banyak sehingga menimbulkan *concerns* dari sisi security.</br>
Dengan adanya perkembangan di versi kubernetes berupa fitur *Role Based Access Control* (RBAC) dan *Custom Resource Definitions* (CRD) maka pada Helm3, komponen Tiller dihilangkan. Akses dan privilege user/tool dapat dengan mudah diatur menggunakan RBAC sehingga tidak dibutuhkan lagi komponen perantara seperti Tiller.

- 3-Way Strategic Merge Patch
Helm2 memiliki fitur *Snapshot* berupa revision yang memudahkan user untuk melakukan install, upgrade dan rollback deployment. Tetapi proses rollback pada Helm2 memiliki keterbatasan, yaitu hanya melakukan compare antara *current chart revision* dan *previous chart revision* dalam mencari perbedaan (diff) sebelum melakukan rollback.</br>
Hal ini dapat menimbulkan masalah ketika ada case berikut:
  1. Deploy app menggunakan Helm.
  1. Ubah deployment secara manual menggunakan kubectl.
  1. Rollback app menggunakan Helm.</br>
Helm2 tidak mendeteksi adanya changes karena tidak melihat *live state* dari deployment yang telah diubah secara manual. Sehingga Helm2 tidak bisa melakukan rollback pada case ini.</br>
Pada Helm3 selain melakukan compare antara *current chart revision* dan *previous chart revision*, Helm3 juga melakukan compare dengan *live state* dari deployment. Sehingga Helm3 masih dapat melakukan rollback jika didapati case seperti diatas yang deploymentnya telah diubah secara manual.</br>
Selain ketika rollback, problem seperti ini juga bisa didapati ketika upgrade. Helm2 tidak akan menyimpan (preserve) perubahan yang dilakukan manual sebelum upgrade. Tetapi Helm3 dapat *preserve* perubahan manual yang dilakukan sebelum upgrade sehingga akan tetap ada setelah upgrade.

Selain kedua hal ini, beberapa perbedaan lain antara Helm2 dan Helm3 dapat dilihat pada referensi berikut ini:
[changes_since_helm2](https://helm.sh/docs/faq/changes_since_helm2/).