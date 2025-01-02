---
title: Komponen Helm
description: 
published: true
date: 2025-01-02T08:15:13.443Z
tags: containers, helm
editor: markdown
dateCreated: 2025-01-02T08:15:13.443Z
---

Helm mempunyai beberapa komponen untuk dapat bekerja. Mulai dari **helm cli** untuk mengeksekusi **helm command**, **helm charts** yang berisi instruksi dan konfigurasi untuk mendeploy aplikasi, **chart repository** untuk menyimpan dan mendistribusikan chart, **helm release**  yang menunjukkan deployment dari aplikasi, dan beberapa komponen pendukung lainnya.

Konsep sederhana dari beberapa komponen Helm ini:
Helm akan menginstal chart ke kubernetes cluster, lalu akan terbentuk release yang mempunyai nama dan revision number tersendiri. Helm chart yang diinstal dapat kita temukan di chart repositories.

## Helm Charts
Helm charts merupakan kumpulan dari beberapa file yang berisi tentang aplikasi yang akan kita deploy ke kubernetes. Di dalam charts kita dapat membuat template dari deployment, konfigurasi deployment, sampai ke versioning deployment.

## Helm Releases
Ketika chart di aplikasikan di kubernetes, maka helm akan membuat *releases* yang mempunyai nama tersendiri. Karena itu, kita dapat mendeploy aplikasi dengan chart yang sama, tetapi mempunyai nama berbeda. Setiap releases mempunyai revision number masing-masing, sehingga kita dapat melakukan perubahan pada setiap releases secara independen.

## Helm Repositories
Sebelum membuat chart baru, kita bisa mencari chart yang diinginkan pada helm chart repositories yang ada di Internet. Ada banyak helm chart yang dibuat oleh komunitas dan bisa langsung kita pakai. Ada banyak juga beberapa helm repository yang dapat kita gunakan. Contoh helm repository publik yang dapat kita manfaatkan adalah [Artifact Hub](https://artifacthub.io/).