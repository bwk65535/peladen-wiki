---
title: Konsep Dasar Jaringan Komputer
description: 
published: true
date: 2024-11-10T08:00:14.467Z
tags: networking
editor: markdown
dateCreated: 2024-11-10T07:55:44.928Z
---

## Pengertian Jaringan
Jaringan adalah suatu kumpulan perangkat yang saling terhubung dan dapat melakukan komunikasi satu sama lain. Perangkat yang terhubung ke jaringan dapat berupa End Devices, seperti PC, Laptop, Smartphone, server, dll., maupun berupa Intermediary Devices, seperti router, switch, access point, dll.

Suatu jaringan dapat berisi mulai dari dua buah komputer saja, hingga jaringan besar seperti internet. Berikut beberapa komponen dasar yang membentuk suatu jaringan :
- End Devices : perangkat akhir yang menggunakan jaringan, seperti PC dan server.
- Interconnection : penghubung yang mengirimkan data antar perangkat jaringan, seperti kabel, network interface card (NIC), konektor, dll.
- Intermediary Devices : perangkat yang meneruskan/memforward data antar end devices, seperti switch, router, dll.
      
## Tujuan dan Manfaat Jaringan
Tujuan utama sebuah jaringan yaitu membawa data dan informasi dari satu perangkat ke perangkat lainnya secara tepat tanpa ada kesalahan. Dengan adanya jaringan, kita dapat mendapatkan banyak manfaat seperti berikut :
- Membagi sumber daya (resources) yang ada, misalnya daripada membeli banyak printer untuk beberapa komputer, lebih baik membeli satu printer yang berkualitas tinggi untuk kemudian di-share melalui jaringan.
- Mempercepat proses pemindahan data, daripada menggunakan media eksternal seperti CD atau Flash disk untuk mengkopi data ke komputer lain, akan lebih cepat jika data tersebut di-share melalui jaringan.
- Menggunakan aplikasi secara terpusat, aplikasi dapat diinstall hanya pada server saja, dan komputer lain berfungsi sebagai client yang dapat mengaksesnya bersama-sama.

## Topologi Jaringan
Topologi di dalam merupakan suatu diagram untuk menjelaskan hubungan antar perangkat dan bentuk dari suatu jaringan. Topologi ada 2 macam :
1. Topologi fisik, menggambarkan bagaimana perangkat dalam jaringan terhubung secara fisik dan bagaimana interkoneksinya, seperti jalur kabel, jalur sinyal wireless, dll.
2. Topologi logika, menggambarkan bagaimana jalur data (trafik) berpindah di dalam jaringan fisik.

Ada beberapa tipe topologi fisik :
- Topologi Bus
Topologi ini hanya menggunakan satu kabel coaxial sebagai penghubung di jaringan. Semua komputer di dalam jaringan terhubung ke kabel tersebut, bisa menggunakan konektor T maupun di hubungkan langsung. Jika kabel utamanya rusak, maka seluruh jaringan mati.

![topologi bus](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/bus-topology.png "Topologi bus")

- Topologi Ring
Hampir sama dengan topologi bus, bedanya perangkat yang berada di ujung topologi dihubungkan ke perangkat di ujung yang lain, sehingga menyerupai lingkaran.

![topologi ring-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/ring-topology-1.png "Topologi ring-1")
![topologi ring-2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/ring-topology-2.png "Topologi ring-2")

- Topologi Star
Semua komputer terhubung ke perangkat sentral hub/switch sebagai penghubungnya, sehingga komputer yang ingin mengirim data harus mengirim ke hub/switch terlebih dahulu. Jaringan ini sudah menggunakan kabel UTP. Jika perangkat sentralnya mati, maka semua perangkat yang terhubung dengannya tidak dapat mengirim data.
![topologi star](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/star-topology.png "Topologi star")

- Topologi Full Mesh
Topologi ini digunakan jika ingin mendapatkan tingkat reliabilitas yang tinggi di jaringan. Pada contoh, terlihat apabila ada satu jalur yang rusak, maka masih dapat menggunakan jalur yang lain. Kekurangannya topologi ini lebih mahal implementasinya dan memerlukan konfigurasi yang lebih kompleks.

![topologi full mesh](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/full-mesh-topology.png "Topologi full mesh")

- Topologi Partial Mesh
Mirip seperti full mesh, akan tetapi ada perangkat yang hanya terhubung ke beberapa perangkat saja, tidak ke semua perangkat. Topologi ini dapat digunakan jika topologi full mesh dirasa terlalu mahal atau sulit untuk diterapkan.

![topologi partial mesh](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/partial-mesh-topology.png "Topologi partial-mesh")