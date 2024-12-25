---
title: 7 Layers OSI
description: 
published: true
date: 2024-11-10T10:39:39.316Z
tags: networking
editor: markdown
dateCreated: 2024-11-10T10:39:33.010Z
---

Pada masa awal berkembangnya teknologi jaringan, setiap vendor perangkat jaringan mempunyai teknologi dan solusi tersendiri untuk perangkat jaringan yang dibuatnya. Hasilnya, setiap perangkat yang berbeda vendor tidak kompatibel satu dengan yang lain. Dari sinilah muncul ide untuk membuat suatu standar referensi yang disetujui semua vendor sehingga semua perangkat jaringan dapat kompatibel satu dengan yang lain.

Organisasi ISO (International Organization for Standardization) kemudian meneliti beberapa pemodelan network yang kemudian melahirkan model OSI (Open System Interconnection) yang dikeluarkan tahun 1984. OSI menggunakan pendekatan berlapis (layered approach), sehingga memudahkan vendor perangkat jaringan untuk membuat hardware untuk layer tertentu dan membuat pengembang software dapat membuat aplikasi untuk layer aplikasi saja, tidak perlu memperhatikan semua layer.

Singkatnya OSI model digunakan sebagai referensi dalam pembuatan protokol yang dapat digunakan pada sistem yang independent dan juga untuk memudahkan dalam memahami bagaimana data sebenarnya berjalan di dalam suatu jaringan. Setiap perubahan data pada masing-masing layer dapat dipelajari secara terpisah.

OSI terdiri dari 7 layer seperti berikut :
- Layer 1 - Physical
Mendeskripsikan segala sesuatu yang bersifat fisik, seperti konektor, kabel, persinyalan, bit, dan beberapa atribut fisik lainnya.
- Layer 2 - Data Link
Bertugas untuk memformat data ke dalam media fisik. Juga dapat mendeteksi error dalam pengiriman sinyal data dan memastikan sinyal dikirim dengan baik. Pada layer ini terdapat alamat fisik seperti MAC address. Contoh protokol yang ada di layer ini adalah Ethernet.
- Layer 3 - Network
Layer ini bertugas untuk melakukan routing (pemilihan jalur) dan konektivitas ke perangkat tujuan. Pada layer ini terdapat alamat logika, seperti IP address. Contoh protokol pada layer ini IPv4 dan IPv6.
- Layer 4 - Transport
Layer ini bertugas menjalin komunikasi dengan perangkat tujuan, memecah data menjadi segmen (segmentasi) dan penyusunan data kembali (reassembly). Pada layer ini terdapat alamat port, yang menjadi identifikasi bagi aplikasi yang terhubung ke jaringan. Contoh protokol pada layer ini TCP dan UDP.
- Layer 5 - Session
Layer ini bertugas membuka, mengatur, dan menutup sesi komunikasi antar perangkat yang sedang berhubungan. Misalnya suatu web server akan tahu bahwa ada client 1 menggunakan sesi 1, client 2 menggunakan sesi 2, sehingga web server dapat memisahkan komunikasi antar client 1 dan 2. Contoh protokol pada layer ini RPC, API, NFS, dll.
- Layer 6 - Presentation
Layer ini bertugas memformat dan menstruktur data sehingga data dapat  ditampilkan dengan baik oleh aplikasi. Contohnya pemformatan JPEG, MPEG, ASCII, layanan VNC, RDP, presentasi little endian, big endian, dll.
- Layer 7 - Application
Di layer inilah adanya aplikasi yang yang berhubungan langsung dengan user, seperti smtp, http, ftp, telnet, dll.

## Enkapsulasi, Dekapsulasi & PDU
Data yang diproses dari layer application hingga layer physical akan mengalami proses enkapsulasi, yaitu pembungkusan/penambahan data dengan header yang ada pada tiap-tiap layer. Sedangkan data yang diproses dari physical layer hingga application layer akan mengalami proses dekapsulasi, yaitu pembuangan header tiap-tiap layer ketika data diteruskan ke layer di atasnya.

Data yang telah dienkapsulasi pada tiap layer memiliki "sebutan" atau PDU (Protocol Data Unit) yang berbeda-beda.

![7-layer-osi](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/7-layer-osi.png "7-layer-osi")