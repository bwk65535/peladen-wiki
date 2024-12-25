---
title: IP Address versi 4
description: 
published: true
date: 2024-11-17T09:48:13.367Z
tags: networking
editor: markdown
dateCreated: 2024-11-17T09:48:05.753Z
---

## Konversi Bilangan Biner
Sebelum kita dapat melakukan penghitungan subnet ip address, kita harus lebih dulu memahami bilangan biner dan cara mengkonversi bilangan biner ke bilangan desimal maupun sebaliknya.

Seperti telah kita ketahui sebelumnya, ip address versi 4 memiliki 32 bit yang dibagi menjadi 4 bagian. Satu bagiannya memiliki 8 bit sehingga disebut oktet. Untuk melakukan subnetting, kita harus "meminjam" bit host ID pada ipv4 untuk digunakan sebagai bit network ID. Sehingga kita mendapatkan beberapa subnet sesuai dengan bit host ID yang kita gunakan.

Untuk memudahkan, kita dapat membuat tabel nilai bit pada biner jika dikonversi menjadi desimal. Karena tiap oktet pada ipv4 bernilai 8 bit, kita cukup membuat tabelnya untuk 8 bit saja.

|         |  2<sup>7</sup> | 2<sup>6</sup> | 2<sup>5</sup> | 2<sup>4</sup> | 2<sup>3</sup> | 2<sup>2</sup> | 2<sup>1</sup> | 2<sup>0</sup> |
|---------|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| Desimal | 128 | 64 | 32 | 16 |  8 |  4 |  2 |  1 |
|         |     |    |    |    |    |    |    |    |

Bit yang paling kiri disebut juga most significant bit (MSB), karena bit disebelah kiri memiliki nilai yang lebih besar dari bit disebelah kanannya. Bit yang paling kanan disebut least significant bit (LSB) karena bit disebelah kanan memiliki nilai yang lebih kecil dari bit di sebelah kirinya.

### Konversi biner ke desimal
Sebagai contoh, kita ingin mengubah biner 11001001 menjadi desimal. Masukkan bilangan biner tersebut ke tabel secara berurutan.

| Desimal | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---------|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 201 | 1 | 1 | 0 | 0 | 1 | 0 | 0 | 1 |

**Abaikan bit yang bernilai 0, jumlahkan bit yang bernilai satu**. Dari contoh diatas dapat kita hitung: 128+64+8+1 = 201

### Konversi desimal ke biner
Sebaliknya jika ingin mengkonversi nilai desimal ke biner, kurangi nilai desimal dengan nilai bit biner dari yang paling besar sampai hasilnya 0. **Berikan nilai 1 jika nilai desimal tersebut lebih besar atau sama dengan nilai bit biner tersebut, dan beri nilai 0 jika nilai desimal tersebut lebih kecil dari nilai bit binernya**.

Misalnya nilai desimal 228 kita konversi menjadi bilangan biner seperti berikut:
228 lebih besar dari 128, beri nilai 1 pada kolom 128, lalu kurangi 228-128 = 100

| Desimal | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---------|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 228 | 1 |   |   |   |   |   |   |   |

100 > 64, beri nilai 1 pada kolom 64, lalu kurangi 100-64 = 36
36 > 32, beri nilai 1 pada kolom 32, lalu kurangi 36-32 = 4
4 < 16, beri nilai 0 pada kolom 16, lalu lewati nilai bit tersebut dan kurangi dengan nilai bit selanjutnya

| Desimal | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---------|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 228 | 1 | 1 | 1 | 0 |   |   |   |   |

4 < 8, beri nilai 0 pada kolom 8
4 = 4, beri nilai 1 pada kolom 4
sisanya 4-4 = 0, sehingga beri nilai 0 pada kolom yang tersisa

| Desimal | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|---------|:---:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| 228 | 1 | 1 | 1 | 0 | 0 | 1 | 0 | 0 |

Dari tabel diatas, dapat diketahui hasil konversi dari bilangan desimal 228 adalah 11100100.

## Pengenalan IPv4
IP address merupakan jenis pengalamatan logika pada jaringan TCP/IP. Semua perangkat jaringan yang terhubung ke Internet sekarang ini menggunakan pengalamatan IP. IP beroperasi pada layer 3 pada model OSI. IP berfungsi untuk memberi alamat secara unik pada setiap perangkat yang terhubung ke jaringan.

Jenis ip address yang banyak digunakan sekarang ini adalah IPv4 dan IPv6. IPv4 merupakan alamat yang mempunyai panjang 32 bit biner dan terdiri dari dua bagian, yaitu network id dan host id.

![IP Address sections](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/ip-address-network-host-id.png "IP Address sections")

Penulisan IP address dalam bentuk desimal dibagi menjadi 4 bagian, sering disebut juga bentuk dotted-decimal. Masing-masing bagian tersebut berisi 8 bit biner, sehingga setiap bagian tersebut sering disebut oktal/oktet. 

![IP Address sections](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/ip-biner-to-decimal.png "IP Address sections")

## Network, Host, and Broadcast address
Ada 3 macam penggunaan ip address pada sebuah jaringan, yaitu sebagai network address, host address, dan broadcast address.

1. Network Address
Network address merupakan ip address yang digunakan untuk menunjukkan alamat dari suatu jaringan. Semua bit host id pada network address bernilai 0, artinya network address merupakan alamat ip pertama pada suatu jaringan. Network address tidak dapat digunakan sebagai alamat perangkat jaringan.

    ![Network address](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/network-address.png "Network address")

2.  Broadcast Address
Broadcast address merupakan ip address yang digunakan sebagai alamat broadcast pada suatu jaringan. Paket yang ditujukan ke alamat broadcast akan diterima oleh semua host/perangkat jaringan di jaringan tersebut. Semua bit host id pada broadcast address bernilai 1, artinya broadcast address merupakan alamat ip terakhir pada suatu jaringan. Sama seperti network address, broadcast address tidak dapat digunakan sebagai alamat perangkat jaringan.

    ![Broadcast address](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/broadcast-address.png "Broadcast address")

3. Host Address
Host address merupakan alamat ip yang dapat diberikan pada setiap perangkat jaringan, seperti komputer maupun interface router. Host address berfungsi sebagai identitas suatu host di dalam jaringan. Semua alamat ip yang terletak di antara network address dan broadcast merupakan host address.

| Type    | Address                   |
|--------------------|---------------------------------|
| Network address    | 192.168.100.0                   |
| Broadcast address  | 192.168.100.255                 |
| Range host address | 192.168.100.1 – 192.168.100.254 |

## Netmask / Prefix-length
Netmask merupakan suatu nilai yang menentukan bagian mana yang merupakan network id dan host id pada suatu ip address. Netmask mempunyai bilangan 32 bit biner yang menunjukkan nilai 1 pada bagian ip address yang merupakan network id dan menunjukkan nilai 0 pada bagian ip address yang menunjukkan host id.

![Netmask](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/netmask.png "Netmask")

Netmask dapat juga ditulis dengan notasi prefix-length, yaitu dengan menuliskan jumlah bit network id. Misalnya alamat ip dengan prefix-length /24, berarti 24 bit pertama pada suatu ip address menunjukkan network id, dan 8 bit sisanya menunjukkan host id. Contoh lain:

| Netmask    | Prefix |
|------------|--------|
| Netmask 255.0.0.0 | /8 |
| Netmask 255.255.0.0 | /16 |
| Netmask 255.255.255.0 | /24, dan seterusnya |

Dengan mengetahui netmask/prefix-length, kita dapat mengetahui network address dari suatu alamat ip, dengan cara melakukan operasi biner AND antara ip address dengan netmasknya. Pada operasi AND, akan dihasilkan nilai 1 apabila kedua nilai yang dioperasikan bernilai 1.

Sebagai contoh, kita mempunyai ip address 192.168.100.125/24, lalu kita ingin mencari network addressnya. Pertama rubah dahulu ip tersebut ke bilangan biner.
```
IP address 192.168.100.125 = 11000000.10101000.01100100.01111101
Netmask /24 = 255.255.255.0 = 11111111.11111111.11111111.00000000
```
Lalu lakukan operasi AND pada kedua barisan biner tersebut

![Netmask AND](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/netmask-and.png "Netmask AND")

Bilangan biner yang dihasilkan dari operasi AND tersebut adalah `11000000.10101000.01100100.00000000`.Selanjutnya kita ubah kembali menjadi desimal menjadi `192.168.100.0`. Itu berarti IP 192.168.100.125/24 merupakan bagian dari network 192.168.100.0/24.

## IP address Public & Private
Penggunaan IP address dibagi menjadi dua bagian besar, yaitu ip address untuk public dan private. IP public digunakan jika kita ingin perangkat jaringan kita terhubung ke Internet. IP public bersifat unik di Internet yang berarti tidak ada dua atau lebih perangkat jaringan yang menggunakan ip yang sama di Internet.

Penggunaan ip public tidak bisa sembarangan, karena pengalokasian ip public diatur oleh Internet Assigned Numbers Authority (IANA) dan regional internet registry di masing-masing kawasan. Untuk Indonesia, pengalokasian ip public sekarang ini dipegang oleh Indonesia Network Information Center (ID-NIC). Semua ip address, kecuali yang termasuk ip address private dan beberapa ip khusus, merupakan alamat ip public.

IP address private merupakan ip yang dapat kita gunakan secara bebas di jaringan kita sendiri. IP private tidak beredar di Internet, sehingga setiap pengguna jaringan dapat menggunakan ip private yang sama di masing-masing jaringan yang dimilikinya. Jaringan private yang ingin terhubung ke Internet tetap harus memiliki ip public dan jaringan tersebut harus memiliki perangkat yang dapat mengubah/menerjemahkan alamat ip private menjadi public (Network Address Translation / NAT).

Berikut daftar ip address private :

| Network Address | Range |
| --------------- | ----- |
| 10.0.0.0/8 | 10.0.0.0 – 10.255.255.255 |
| 172.16.0.0/12 | 172.16.0.0 – 172.31.255.255 |
| 192.168.0.0/16 | 192.168.0.0 – 192.168.255.255 |

### Alamat IP khusus
Ada beberapa alamat ip yang digunakan untuk keperluan tertentu, sehingga tidak dapat digunakan sebagai host address.

1. Local broadcast / Limited broadcast address
IP address 255.255.255.255 merupakan alamat ip yang digunakan sebagai alamat broadcast untuk sebuah jaringan. Paket dengan alamat ini tidak adak diteruskan oleh router ke luar jaringan, sehingga paket dengan alamat ini hanya dikirimkan ke semua host yang masih berada dalam satu jaringan.

2. Loopback address
IP address 127.0.0.0/8 (127.0.0.1 – 127.255.255.255) merupakan alamat loopback pada jaringan. Alamat loopback berfungsi untuk menunjuk ke perangkat jaringan itu sendiri. Alamat loopback tidak dapat dikonfigurasi pada interface fisik, sehingga tidak dapat digunakan sebagai host address.

## Tipe pengalamatan IPv4
Tipe pengalamatan ip address ada 2 macam, yaitu secara classful dan classless :
### Classful
Pengalamatan classful membagi ip address menjadi beberapa kelas ip, yaitu :
- Kelas A
  - Bit pertama bernilai 0
  - Mempunyai range 0.0.0.0 hingga 127.255.255.255
  - Mempunyai netmask 255.0.0.0 atau /8, artinya 8 bit pertama digunakan sebagai network id, dan 24 bit selanjutnya digunakan untuk host-id.
          
    ![IP kelas A](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/ip-kelas-a.png "IP kelas A")
          
  - Jumlah alamat jaringan : 127 (alamat 127 digunakan untuk loopback)
  - Jumlah alamat host : 16.777.214

- Kelas B
  - Dua bit pertama bernilai 10
  - Mempunyai range 128.0.0.0 hingga 191.255.255.255
  - Mempunyai netmask 255.255.0.0 atau /16
          
    ![IP kelas B](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/ip-kelas-b.png "IP kelas B")
          
  - Jumlah alamat jaringan : 16.384
  - Jumlah alamat host : 65.534

- Kelas C
  - Tiga bit pertama bernilai 110
  - Mempunyai range 192.0.0.0 hingga 223.255.255.255
  - Mempunyai netmask 255.255.255.0 atau /24
          
    ![IP kelas C](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/ip-kelas-c.png "IP kelas C")
          
  - Jumlah alamat jaringan : 2.097.152
  - Jumlah alamat host : 254

- Kelas D
  - Empat bit pertama bernilai 1110
  - Mempunyai range 224.0.0.0 hinga 239.255.255.255
  - Digunakan sebagai alamat multicast
   
- Kelas E
  - Empat bit pertama bernilai 1111
  - Mempunyai range 240.0.0.0 hingga 255.255.255.255
  - Digunakan untuk keperluan experimental, tidak bisa digunakan sembarangan.
          
### Classless
Ketika ip address sudah banyak digunakan di Internet, maka tipe pengalamatan classful menjadi tidak efisien karena banyaknya alokasi ip address yang terbuang. Misalnya jika ada sebuah perusahaan mendapatkan alokasi ip address kelas A yang mempunyai jumlah ip untuk host 16.777.214, dan dikantornya hanya ada sekitar 1000 perangkat, maka akan banyak ip address yang tidak terpakai.

Pada pengalamatan classless atau disebut juga Classless Inter Domain Routing (CIDR), ip address kelas A, B, dan C tidak lagi memiliki panjang netmask yang ditentukan, yaitu /8, /16, dan /24 akan tetapi semua ip address di kelas A, B, dan C bisa bebas menggunakan semua panjang netmask yang diinginkan.

## Subnetting
Subnetting merupakan cara membagi sebuah network address menjadi beberapa network address yang lebih kecil (sub network). Tujuan dilakukannya subnetting adalah untuk efisiensi penggunaan ip address. Suatu blok ip address dapat dibuat sesuai dengan jumlah host address yang diperlukan.

Subnetting dapat dilakukan dengan cara meminjam bit pada bagian host-id untuk digunakan sebagai network-id pada subnet yang baru. Dengan dipinjamnya bit host-id tersebut, maka jumlah host address akan berkurang sesuai dengan berapa bit yang akan dipinjam.

Sebagai contoh, kita ambil blok ip address 192.168.100.0/24. Network address tersebut akan kita pecah, misalnya menjadi 4 subnetwork. Kita hitung dahulu berapa jumlah host pada network address tersebut sebelum dilakukan subnetting.

Cara menghitung jumlah host address: 

2<sup>h</sup> – 2 ; dimana h = jumlah bit host-id
hitung jumlah bit host-id --> total bit ipv4 - bit network id:  32 – 24  = 8 bit
Jadi jumlah host address: 2<sup>8</sup> – 2 = 256 – 2 = 254 host address

Setelah itu, kita hitung berapa bit host-id yang diperlukan untuk dijadikan network-id bagi subnet yang baru.

Cara menghitung jumlah network/subnet:

2<sup>n</sup> ; dimana n = jumlah bit network-id
Kita akan membuat 4 subnet baru, maka 2<sup>n</sup> = 4
Jadi jumlah bit network-id yang dibutuhkan: n = 2

Selanjutnya kita coba mengambil 2 bit host-id untuk kita jadikan sebagai network-id bagi 4 subnet yang akan dibuat.

![subnetting](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/subnetting.png "subnetting")

2 bit yang dipinjam tersebut dapat menghasilkan 4 kombinasi alamat seperti ini:
```
11000000.10101000.01100100.00000000 = 192.168.100.0
11000000.10101000.01100100.01000000 = 192.168.100.64
11000000.10101000.01100100.10000000 = 192.168.100.128
11000000.10101000.01100100.11000000 = 192.168.100.192
```
Keempat alamat yang didapat tersebut merupakan network address bagi 4 subnet yang baru. Karena jumlah bit network-id bertambah 2, maka prefix-length untuk 4 subnet yang baru itu menjadi /26 (awalnya 24 ditambah 2 menjadi 26). Jika dibuat menjadi bentuk netmask menjadi:

`11111111.11111111.11111111.11000000 = 255.255.255.192`

Sehingga hasil subnetting dari network address 192.168.100.0/24 menjadi 4 subnetwork dapat dilihat seperti dibawah ini.

| Network address | Host address | Broadcast address | Netmask |
| --------------- | ------------ | ----------------- | ------- |
| 192.168.100.0 | 192.168.100.1 – 192.168.100.62 | 192.168.100.63 | 255.255.255.192 (/26) |
| 192.168.100.64 | 192.168.100.65 – 192.168.100.126 | 192.168.100.127 | 255.255.255.192 (/26) |
| 192.168.100.128 | 192.168.100.129 – 192.168.100.190 | 192.168.100.191 | 255.255.255.192 (/26) |
| 192.168.100.192 | 192.168.100.193 – 192.168.100.254 | 192.168.100.255 | 255.255.255.192 (/26) |

## Variable Length Subnet Mask (VLSM)
VLSM merupakan pengembangan dari teknik subnetting biasa. Pada teknik subnetting biasa, pembagian IP masih dirasa kurang efisien jika jumlah host address yang dibutuhkan pada masing-masing subnet berbeda jauh. Jika menggunakan VLSM, subnet dari hasil subnetting sebelumnya dapat dilakukan subnetting kembali disesuaikan dengan jumlah host address yang dibutuhkan.

Sebagai contoh, kita akan melakukan teknik subnetting biasa pada jaringan dibawah ini:

Network address yang dimiliki 192.168.200.0/24, akan dibagi menjadi 5 subnet, yang masing-masing subnet dibutuhkan host address sebanyak :
```
Subnet 1 : 29 host address
Subnet 2 : 12 host address
Subnet 3 : 6 host address
Subnet 4 : 2 host address
Subnet 5 : 2 host address
```
Yang pertama kita lakukan adalah menghitung berapa bit yang dibutuhkan untuk membuat 5 subnet.
```
21 = 2
22 = 4
23 = 8
```
Hasil yang paling mendekati jumlah yang dibutuhkan (5 subnet) adalah 8. Sehingga kita harus meminjam 3 bit dari host address untuk dijadikan network address.

![vlsm](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/02/vlsm.png "vlsm")

3 bit yang dipinjam tersebut dapat menghasilkan 8 kombinasi alamat subnet berikut ini :
```
11000000.10101000.11001000.00000000 = 192.168.200.0
11000000.10101000.11001000.00100000 = 192.168.200.32
11000000.10101000.11001000.01000000 = 192.168.200.64
11000000.10101000.11001000.01100000 = 192.168.200.96
11000000.10101000.11001000.10000000 = 192.168.200.128
11000000.10101000.11001000.10100000 = 192.168.200.160
11000000.10101000.11001000.11000000 = 192.168.200.192
11000000.10101000.11001000.11100000 = 192.168.200.224
```
Kita ambil 5 subnet dari hasil subnetting tersebut untuk digunakan pada jaringan.

| Network address | Jumlah host address (2n – 2) | Host address yang dibutuhkan | Sisa host address |
| --------------- | ---------------------------- | ---------------------------- | ----------------- | 
| 192.168.200.0/27 | 30 | 29 | 1 |
| 192.168.200.32/27 | 30 | 12 | 18 |
| 192.168.200.64/27 | 30 | 6 | 24 |
| 192.168.200.96/27 | 30 | 2 | 28 |
| 192.168.200.128/27 | 30 | 2 | 28 |

Dari hasil subnetting tersebut, dapat terlihat ada banyak host address yang tidak terpakai, terutama pada subnet 4 dan 5, yang masih tersisa 28 host. Sedangkan jumlah subnet yang tidak digunakan ada 3, yang masing-masing dapat menyediakan kebutuhan 30 host address.

Selanjutnya, kita akan coba melakukan subnetting dengan teknik VLSM. Pada VLSM, suatu network address akan dipecah berdasarkan host address yang dibutuhkan, mulai dari subnet yang paling besar, hingga yang paling kecil. Contoh yang digunakan masih sama seperti diatas, yaitu network address 192.168.200.0/24 akan dibagi menjadi 5 subnet.

Pertama kita hitung dahulu host address yang dibutuhkan untuk tiap subnet sehingga kita dapat mengetahui berapa netmask untuk tiap-tiap subnet yang akan kita buat.

| Subnet | Host address yang dibutuhkan | Bit host ID | Jumlah host adddress (2n -2) | Prefix-length |
| ------ | ---------------------------- | ----------- | ---------------------------- | ------------- |
| 1 | 29 | 5 | 30 | /27 |
| 2 | 12 | 4 | 14 | /28 |
| 3 | 6 | 8 | 6 | /29 |
| 4 | 2 | 2 | 2 | /30 |
| 5 | 2 | 2 | 2 | /30 |

Setelah itu, kita bisa melakukan subnetting satu per satu, dimulai dari subnet yang paling besar. Disini langsung diperlihatkan penghitungan pada oktet ke 4:
```
Subnet 1 (192.168.200.0/24) --> /27
00000000 = 192.168.200.0 --> terpakai untuk subnet 1
00100000 = 192.168.200.32 --> dilakukan subnetting kembali untuk subnet 2
01000000 = 192.168.200.64
01100000 = 192.168.200.96
10000000 = 192.168.200.128
10100000 = 192.168.200.160
11000000 = 192.168.200.192
11100000 = 192.168.200.224
```
dari hasil subnetting yang didapat, dilakukan proses subnetting kembali untuk subnet berikutnya.
```
Subnet 2 (192.168.200.32/27) --> /28
00100000 = 192.168.200.32 --> terpakai untuk subnet 2
00110000 = 192.168.200.48 --> dilakukan subnetting kembali untuk subnet 3
```
Untuk subnet ke 3, dapat menggunakan hasil subnet pada subnetting ke 2 ( jika mencukupi), atau dapat menggunakan hasil subnet pada subnetting ke 1.
```
Subnet 3 (192.168.200.48/28) --> /29
00110000 = 192.168.200.48 --> terpakai untuk subnet 3
00111000 = 192.168.200.56 --> dilakukan subnetting kembali untuk subnet 4 dan 5

Subnet 4 dan 5 (192.168.200.56/29) --> /30
00111000 = 192.168.200.56 --> terpakai untuk subnet 4
00111100 = 192.168.200.60 --> terpakai untuk subnet 5
```
Berikut hasil subnetting untuk 5 network diatas:

| Network address | Jumlah host address (2n – 2) | Host address yang dibutuhkan | Sisa host address |
| --------------- | ---------------------------- | ---------------------------- | ----------------- |
| 192.168.200.0/27 | 30 | 29 | 1 |
| 192.168.200.32/28 | 14 | 12 | 2 |
| 192.168.200.48/29 | 6 | 6 | 0 |
| 192.168.200.56/30 | 2 | 2 | 0 |
| 192.168.200.60/30 | 2 | 2 | 0 |

Dapat terlihat perbedaan hasil subnetting yang menggunakan VLSM seperti pada tabel diatas. Hanya ada sedikit sisa host address yang tidak terpakai, sehingga pembagian subnet yang dilakukan menjadi lebih efisien.
           
## Supernet / Summarization
Teknik supernetting atau biasa disebut juga summarization merupakan kebalikan dari subnetting. Jika subnetting merupakan cara untuk memecah satu jaringan menjadi beberapa jaringan, maka supernetting merupakan cara menggabungkan atau meringkas beberapa alamat jaringan menjadi satu alamat jaringan.

Pada umumnya alamat supernet digunakan untuk meringkas route pada saat mengisi address list atau access-list dan bisa juga digunakan ketika melakukan routing statik. Dengan supernetting, beberapa alamat jaringan dapat diarahkan atau di-refer ke satu alamat jaringan saja.

Sebagai contoh, kita punya 4 alamat jaringan:
```
192.168.100.0/24 netmask 255.255.255.0
192.168.101.0/24 netmask 255.255.255.0
192.168.102.0/24 netmask 255.255.255.0
192.168.103.0/24 netmask 255.255.255.0
```
lihat oktet dimana angkanya berbeda. Pada contoh diatas, oktet pertama dan kedua angkanya sama (192.168). Oktet ketiga angkanya berbeda (100, 101, 102, 103), maka pada oktet ketiga ini alamatnya bisa kita lakukan supernetting.

Ubah oktet ketiga menjadi bilangan biner, lalu lihat sampai mana angka bilangan binernya berbeda.
```
100 = 011001 00
101 = 011001 01
102 = 011001 10
103 = 011001 11
```
terlihat bilangan binernya sama hingga digit ke 6. Ambil 6 digit pertama, lalu beri nilai 0 hingga bit ke-32.
```
0110001 00.00000000
011000100.00000000 --> diubah ke desimal menjadi 100.0
```
Dari sini didapat alamat jaringannya setelah dilakukan supernetting adalah 192.168.100.0. Lalu bagaimana cara menghitung netmask/prefix-lengthnya?

Prefix-length yang menunjukkan banyaknya bit network address adalah sampai bit dimana bilangan binernya sama. Terlihat diatas bilangan binernya sama hingga di **digit ke 6 di oktet ketiga**, berarti secara keseluruhan bilangan binernya **sama hingga bit ke-22** (8+8+6).

Hasilnya, keempat alamat jaringan diatas dapat diringkas menjadi 192.168.100.0/22 netmasknya 255.255.255.252.

Contoh lain:
```
10.14.33.208/24
10.14.33.222/24
10.14.33.199/24
10.14.33.230/24

208 = 11 010000
222 = 11 011110
199 = 11 000111
230 = 11 100110
```
ambil 2 bit 11, lalu tambahkan 0 hingga akhir
```
11000000 = 192
```
Untuk netmask, binernya sama hingga bit ke 26 (8+8+8+2 = 26).
Alamat supernetnya menjadi 10.14.33.192/26 netmask 255.255.255.192.
