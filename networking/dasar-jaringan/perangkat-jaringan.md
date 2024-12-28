---
title: Perangkat Jaringan
description: 
published: true
date: 2024-12-28T07:08:16.679Z
tags: networking
editor: markdown
dateCreated: 2024-11-17T09:38:29.655Z
---

## Pengkabelan
Ada dua macam tipe pengkabelan :
### Copper (tembaga)
Umumnya pengkabelan tembaga menggunakan jenis kabel UTP (Unshielded Twisted Pair). Yang sekarang ini banyak dipakai dalam instalasi jaringan adalah UTP kategori 5e dan 6.

| Tipe kabel | Max. Speed | Bandwith | Max. Distance |
| ---------- | ---------- | -------- | ------------- |
| Cat. 5 | 100 Mbps | 100 Mhz | 100 m |
| Cat. 5e | 1000 Mbps | 100 Mhz | 100 m |
| Cat. 6 | 1000 Mbps | 250 Mhz | 100 m |
| Cat. 6 | 10000 Mbps | 250 Mhz | 57 m |
| Cat. 6a | 10000 Mbps | 500 Mhz | 100 m |

Konektor yang digunakan untuk terminasi kabel UTP adalah konektor RJ-45. Untuk tipe terminasi kabelnya sendiri ada 2 macam :

![Terminasi UTP](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/terminasi-utp.png "Terminasi UTP")
Sumber : http://networkwhateva.weebly.com/uploads/5/5/6/9/55699197/5942409_orig.png

Jika kita ingin membuat kabel berjenis straight, maka tipe terminasinya harus sama dikedua ujungnya, misal jika kita menggunakan tipe T568B, maka di kedua ujung kabel harus diterminasi dengan tipe T568B. Kabel straight digunakan untuk menghubungkan perangkat yang berbeda jenis, misalnya komputer ke switch, router ke switch, dll.

Tetapi jika kita ingin membuat kabel crossover, maka kedua tipe terminasi harus kita gunakan, di ujung yang satu menggunakan T568B dan diujung satunya T568A. Kabel cross digunakan untuk menghubungkan perangkat yang sejenis, misalnya switch dengan switch, komputer dengan komputer, komputer dengan router, dll.

Jika perangkat yang ingin kita hubungkan sudah mempunyai fitur Auto MDI-X (Auto sensing), maka tidak dibutuhkan lagi kabel cross, pengkabelan perangkat jaringan dapat menggunakan kabel straight saja, dan perangkat akan menyesuaikan secara otomatis.

### Fiber Optic
Ada dua tipe kabel fiber optic:
- Multi mode
Pada kabel jenis ini, ada banyak jalur cahaya yang ditransmisikan, menggunakan sumber cahaya LED. Diameter core biasanya 62,5 micron atau 50 micron. Untuk pengkabelan jarak dekat dan menengah.

    ![Multi Mode Fiber](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/multimode-fiber-path.png "Multi Mode Fiber")
Sumber : cisco SPNGN1101S02L02

- Single mode
Pada kabel ini, hanya ada satu jalur cahaya yang ditransmisikan, sumber cahaya menggunakan laser. Diameter core antara 8-10 micron. Untuk pengkabelan jarak jauh.

    ![Single Mode Fiber](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/singlemode-fiber-path.png "Single Mode Fiber")
Sumber : cisco SPNGN1101S02L02

Ada beberapa konektor yang digunakan dalam pengkabelan fiber optic, contohnya seperti berikut :

![Konektor fiber optic](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/konektor-fiber-optic.jpg "Konektor fiber optic")
Sumber: google search

## Perangkat-perangkat jaringan
- Repeater dan Hub
Repeater dan hub merupakan perangkat layer 1 di model OSI yang hanya meneruskan sinyal yang masuk lalu mengeluarkannya di semua port yang dimilikinya. Hub mempunyai port yang lebih banyak dari repeater, sehingga disebut juga multiport repeater. Jika menggunakan hub, hanya ada satu perangkat yang dapat mengirim dalam satu waktu, sehingga hub bersifat half duplex. Hub tidak dapat membaca alamat fisik maupun logika, sehingga hub tidak memecah collision domain dan broadcast domain.

    ![Repeater](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/repeater.png "Repeater")

- Bridge
Ketika jaringan sudah semakin besar, maka hub tidak memadai lagi untuk digunakan. Jaringan yang dikoneksikan menggunakan hub akan semakin lambat karena banyaknya paket broadcast dan seringnya terjadi collision. Maka dibuatlah bridge yang berfungsi untuk menghubungkan dua segmen jaringan yang menggunakan hub.</br>
Bridge beroperasi di layer 2 dalam model OSI. Bridge dapat memforward, memfilter dan melakukan flooding frame, karena bridge sudah dapat membaca mac address. Karena kemampuannya itulah bridge dapat memecah collision domain, sehingga frame yang harusnya hanya beredar di suatu segmen tidak membanjiri segmen yang lain.

    ![Bridge](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/bridge.png "Bridge")
       
- Switch
Switch merupakan pengembangan dari bridge. Switch mempunyai port yang lebih banyak daripada bridge sehingga sering disebut juga multiport bridge. Switch dapat bekerja lebih cepat karena dapat memproses frame di hardware. Sekarang ini switch sudah menggantikan hub sebagai penghubung ke end devices, karena setiap port di switch bekerja di layer 2 sehingga dapat membaca mac address dan memecah collision domain di jaringan.</br>
Switch dapat bekerja secara full duplex, yang berarti setiap perangkat yang terhubung ke switch dapat mengirim dan menerima paket dalam waktu yang bersamaan.

    ![Switch](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/switch.png "Switch")

- Router
Router merupakan perangkat yang bekerja di layer 3 pada model OSI. Router dapat membaca IP address, sehingga router dapat melakukan meneruskan paket yang melintasi jaringan yang berbeda. Dalam melakukan pemilihan jalur terbaik, router memiliki routing table yang berisi alamat-alamat jaringan yang diketahuinya. Routing table tersebut diisi bisa secara statik, yaitu dengan konfigurasi manual, bisa juga secara dinamis menggunakan protokol-protokol routing.</br>
Dikarenakan router dapat mengenali ip address dan alamat jaringan, selain memecah collision domain, setiap port router juga dapat memecah broadcast domain.

    ![Router](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2019/08/router.png "Router")
