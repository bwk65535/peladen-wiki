---
title: TCP/IP Protocol Suite
description: 
published: true
date: 2024-11-12T10:54:03.657Z
tags: networking
editor: markdown
dateCreated: 2024-11-12T10:53:57.582Z
---

Model 7-layer OSI hanya digunakan sebagai referensi saja. OSI digunakan untuk menganalisa jaringan dan protokol di dalamnya. Sedangkan TCP/IP merupakan suatu model jaringan yang sekarang digunakan. TCP/IP terdiri dari sekumpulan protokol yang digunakan perangkat jaringan untuk berkomunikasi. Hanya model TCP/IP yang diterima dan digunakan oleh jaringan Internet sekarang ini. 

Nama TCP/IP diambil berdasarkan protokol yang paling populer di dalamnya, yaitu TCP dan IP, tapi bukan berarti kita hanya bisa menggunakan TCP dan IP saja. Pada dasarnya, model OSI dan TCP/IP tidak banyak berbeda, hanya pada TCP/IP, layer application, presentation, dan session digabung menjadi layer application saja. Selain itu, layer data link dan physical pada OSI digabung juga menjadi layer Network Access. Dan layer network pada OSI berubah namanya menjadi layer Internet pada TCP/IP.

![TCP/IP Layer](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/tcpip-vs-osi.png "TCP/IP Layer")

## TCP dan UDP
Pada jaringan TCP/IP, kita mempunyai 2 transport protocol utama :
1. TCP (Transmission Control Protocol)
TCP adalah protokol yang bersifat connection-oriented, dideskripsikan dalan RFC 793. TCP membutuhkan overhead yang lebih dalam pengiriman data karena adanya fungsi-fungsi reliable delivery yang berarti data yang dikirim akan diperiksa apakah sampai ke tempat tujuan atau tidak, flow control yang dapat mengatur banyaknya data yang dapat dikirim dan diterima, error correction, dll. Contoh penggunaan : Web browser, e-mail, file transfer, dll.
2. UDP (User Datagram Protocol)
UDP merupakan protokol yang simple, bersifat connectionless, dan dideskripsikan di RFC 768. UDP mempunyai keunggulan pada overhead pengiriman data yang rendah. Bentuk data dalam komunikasi UDP disebut datagrams. Datagram yang dikirimkan bersifat "best-effort" yang berarti dalam pengiriman data tersebut tidak ada mekanisme untuk mengetahui apakah data berhasil sampai pada tujuannya atau tidak, sehingga datagram dapat diproses lebih cepat oleh aplikasi. Contoh penggunaan : DNS, Video Streaming, VoIP, Game Online, dll.
       
## TCP 3-way handshake
Pada saat komputer akan mengirim data menggunakan TCP, TCP akan membangun koneksi terlebih dahulu dengan komputer tujuan sebelum dilakukan pengiriman data. Proses membangun koneksi ini disebut tcp 3-way handshake.

![TCP 3-way handshake](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/07/tcp-3-way-handshake.png "TCP 3-way handshake")

## Port Number
TCP dan UDP menggunakan alamat port untuk menghubungkan aplikasi dengan jaringan. Pada setiap data yang dikirimkan akan diberikan alamat port pengirim/sumber dan alamat port penerima/tujuan.
Internet Assigned Numbers Authority (IANA) merupakan badan yang berwenang untuk mengalokasikan port numbers. Ada beberapa macam tipe dari port numbers :
- Well Known Port (0-1023)
Port numbers yang tercakup disini sudah dialokasikan untuk services dan aplikasi tertentu. Sehingga aplikasi-aplikasi client yang ingin merequest koneksi ke server dapat diprogram dan diset menuju port-port yang spesifik dan tetap pada server.
- Registered Port (1024-49151)
Port numbers di sini diberikan dapat diberikan untuk user processes atau aplikasi. Jika port-port pada range ini tidak sedang digunakan, dapat juga dipakai secara dinamis oleh client sebagai source port-nya.
- Dynamic or Private Ports (49152-65535)
Disebut juga sebagai ephemeral port, merupakan port-port yang dipakai secara dinamis oleh client ketika menginisiasi koneksi.

Contoh beberapa port yang umum digunakan :
- Port 20-21 : FTP
- Port 22 : SSH
- Port 23 : Telnet
- Port 25 : SMTP
- Port 80 : HTTP
- Port 443 : HTTPS
- dll.