---
title: Ethernet
description: 
published: true
date: 2024-11-17T09:27:13.660Z
tags: networking
editor: markdown
dateCreated: 2024-11-17T09:27:07.351Z
---

Ethernet merupakan satu-satunya protokol yang digunakan pada jaringan LAN sekarang ini. Ethernet bekerja pada layer 1 dan 2 pada model OSI, bedanya Ethernet membagi lagi layer Data Link menjadi 2, sebagai berikut :

![Ethernet Layer](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/ethernet-layer.png "Ethernet Layer")

1. Logical Link Control (LLC), mengatur hubungan dengan network layer dan melakukan error correction.
2. Media Access Control (MAC), disinilah terletak alamat fisik pada jaringan ethernet, yaitu MAC address. Sublayer ini juga mengatur bagaimana data/frame akan ditempatkan pada media transmisi.

## Duplex
Pada jaringan Ethernet, perangkat jaringan dapat mengirim data secara half duplex maupun full duplex.
- Half duplex, hanya ada satu perangkat yang dapat mengirim data dalam satu waktu
- Full duplex, setiap perangkat dapat mengirim maupun menerima data dalam waktu yang bersamaan.

## MAC address
Suatu MAC address terdiri dari 48 bit dan terbagi menjadi beberapa bagian :

![Mac Address frame](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/mac-address-frame.png "Mac Address frame")

1. Broadcast bit, jika sebuah frame ditujukan untuk broadcast, maka bit ini akan bernilai 1.
2. Local bit, bit ini harus diset 1 jika kita ingin merubah MAC address, artinya alamat ini akan bersifat unik pada jaringan lokal kita saja.
3. OUI (Organization Unique Identifier), setiap network vendor mendapatkan 22 bit untuk identifikasi.
4. Vendor, sisa 24 bit terakhir akan digunakan network vendor untuk memberikan alamat yang unik bagi setiap perangkat jaringan yang dibuatnya.

MAC address ditulis dalam bilangan heksadesimal, dapat menggunakan tanda pemisah "-", maupun ":". Contoh MAC address :
```
00-AC-4A-EC-41-42
00:AC:4A:EC:41:42
```

## Address Resolution Protocol (ARP)
Ketika sebuah perangkat jaringan akan mengirim data ke perangkat jaringan yang lain, maka perangkat pengirim harus mengetahui alamat tujuan untuk data yang akan dikirimkannya, baik itu alamat logika (ip address) atau alamat fisik (mac address). Perangkat pengirim sudah mengetahui alamat ip address dirinya sendiri maupun penerima, karena alamat ip tersebut akan dikonfigurasi oleh user atau didapat dari aplikasi. Tapi bagaimana pengirim dapat mengetahui mac address tujuan/penerima ?

Protokol yang bertugas untuk mendapatkan informasi mac address secara dinamis adalah ARP (address resolution protocol). ARP bekerja dengan cara mencari mac address dari alamat IP yang sudah dia punya. Setiap komputer atau perangkat jaringan lainnya mempunyai tabel mac address yang berisikan pasangan ip address dan mac address yang didapat melalui ARP.

Contoh bagaimana cara kerja ARP :
- Komputer A akan mengirim data ke komputer B, akan tetapi komputer A belum mengetahui mac address dari komputer B. Komputer A akan mengirim ARP request yang mempunyai destination mac address FF:FF:FF:FF:FF:FF, yang merupakan alamat broadcast. Itu berarti ARP request ini akan disebar ke seluruh perangkat yang masih berada dijaringan yang sama.

![arp-process-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/arp-process-1.png "arp-process-1")

- Selanjutnya komputer B menerima paket ARP tersebut, karena dia mengetahui ARP tersebut ditujukan ke dirinya (destination ip address = 192.168.1.2). Dari paket tersebut komputer B juga mengetahui ada komputer dengan ip 192.168.1.1 dan memiliki mac address 123, lalu memasukkan informasi tersebut ke tabel ARP yang dimilikinya.
- Komputer B lalu mengirim ARP reply ke komputer A yang berisi source ip address dan source mac address yang dimilikinya. Karena komputer B sudah mengetahui ip address dan mac address komputer A, maka komputer B tidak membroadcast paket ARP reply tersebut, tetapi langsung dikirimkan ke komputer A.

![arp-process-2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/arp-process-2.png "arp-process-2")

- Komputer A lalu memasukkan informasi dari ARP reply tersebut ke tabel ARP yang dimilikinya. Kedua komputer tersebut kini sudah dapat membangun koneksi satu sama lain.

![arp-process-3](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/arp-process-3.png "arp-process-3")
      
**Reverse ARP (RARP)** merupakan kebalikan dari ARP, yaitu protokol yang bertugas untuk mencari ip address berdasarkan mac address yang dimilikinya. Komputer yang melakukan RARP akan mengirimkan paket yang berisi mac address dirinya sendiri secara broadcast, dan RARP server akan menjawaab paket tersebut dengan memberikan ip address yang sesuai.
