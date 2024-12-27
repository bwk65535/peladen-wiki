---
title: Konsep Routing
description: 
published: true
date: 2024-12-27T14:27:46.099Z
tags: networking
editor: markdown
dateCreated: 2024-12-27T14:25:42.898Z
---

Routing dalam jaringan adalah proses mencari jalur terbaik dari satu alamat jaringan untuk menuju alamat jaringan yang lain. Pemilihan jalur terbaik dapat bergantung pada keinginan dari administrator jaringan, protokol routing yang dipergunakan, maupun kebijakan (policy) dari individu/perusahaan yang memiliki jaringan tersebut. 

Perangkat yang melakukan fungsi routing disebut dengan router. Dalam melakukan routing, router akan menggunakan tabel routing yang dimilikinya untuk mencari jalur terbaik. Tabel routing tersebut dapat diisi secara statik (manual) oleh administrator jaringan atau secara dinamis menggunakan protokol routing tertentu.

## Segmentasi Jaringan
Mengapa kita perlu menggunakan routing? Pertanyaan ini tidak lepas dari kebutuhan kita dalam melakukan segmentasi jaringan. Pada awalnya, kita mungkin memiliki jaringan kecil seperti ini saja:

![Segmentasi jaringan 1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/segmen-jaringan-1.png "Segmentasi jaringan 1")

Semakin lama, jumlah perangkat jaringan kita bertambah. Kita mungkin perlu untuk menambah switch untuk perangkat jaringan kita yang lain dan menghubungkan antar switch tersebut.

![Segmentasi jaringan 2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/segmen-jaringan-2.png "Segmentasi jaringan 2")

Dengan makin bertambahnya perangkat yang terhubung ke jaringan dan semakin banyaknya trafik, akan ada masalah yang muncul di jaringan. Beberapa di antaranya adalah:

- Broadcast storm
Paket broadcast merupakan paket yang ditujukan untuk semua perangkat pada jaringan. Contoh protokol yang menggunakan paket broadcast adalah ARP yang digunakan untuk mencari mac address dari ip address yang telah diketahui. Jika jaringan kita semakin besar, maka paket broadcast juga akan semakin banyak beredar dan dapat mengambil bandwidth serta waktu pemrosesan pada jaringan. Hal ini dapat menyebabkan menurunnya kualitas dan performa jaringan.
Akan lebih berbahaya jika pada jaringan kita terdapat looping. Ethernet frame tidak memiliki field TTL (time to live) seperti ip packet. Sehingga jika terjadi looping pada layer 2, maka frame tersebut akan loop selamanya di jaringan hingga kita cabut koneksi kabel secara manual, atau hingga switchnya mengalami crash.
      
    ![Broadcast storm](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/broadcast-storm.gif "Broadcast storm")
      
- Security
Jaringan yang tidak disegmentasi merupakan jaringan flat, artinya semua perangkat berada pada satu jaringan besar yang sama dan dapat saling mengakses satu sama lain. Hal ini cukup berbahaya karena bisa saja ada file atau service penting yang harusnya memiliki akses terbatas menjadi dapat diakses oleh semua orang/perangkat karena tidak adanya access control di level ip (layer 3).
Contoh lain masalah security, apabila ada satu komputer yang terkena virus/malware biasanya akan sangat cepat tersebar ke komputer lain yang berada di satu jaringan yang sama.

- Scalability
Jika perangkat yang terhubung ke jaringan semakin lama semakin banyak, maka kita harus menyediakan alamat yang cukup untuk semua perangkat tersebut. Besarnya blok ip address yang akan dipakai harus diperhitungkan, supaya jaringan tidak kekurangan ip address. IP address akan lebih mudah di manage jika kita membagi jaringan menjadi beberapa blok kecil, dibandingkan dengan satu jaringan yang memiliki blok ip address yang besar.
Selain itu, masalah lain yang akan timbul adalah limitasi ARP table pada perangkat jaringan. Seperti diketahui, perangkat pada  jaringan TCP/IP akan melakukan pencarian mac address dari ip address menggunakan protokol ARP. Hasil pencarian tersebut akan dimasukkan ke dalam ARP table/ARP cache. Semakin banyak perangkat yang berada pada jaringan yang sama, maka ARP table lama kelamaan akan penuh dan perangkat jaringan tersebut tidak dapat menyimpan hasil pencarian ARP ke dalam ARP table. Kondisi ini biasanya disebut ARP table exhaustion/ARP cache overflow.

## Broadcast & Collision Domain
Untuk mengetahui segmen dalam jaringan yang terpisahkan oleh router (routing), switch (bridging), maupun hub (repeater) kita harus memahami apa yang dimaksud dengan broadcast dan collision domain.

Pengertian sederhana dari **collision domain** adalah bagian dari jaringan dimana packet collision (tabrakan paket) dapat terjadi. Packet collision dapat terjadi pada shared network seperti Ethernet yang masih bersifat half duplex, dimana seharusnya hanya ada satu perangkat jaringan yang mengirim paket dalam satu waktu.

Pada contoh gambar di bawah, hub/repeater yang merupakan perangkat layer 1 tidak memecah collision domain. Setiap link/segmen jaringan yang terkoneksi ke hub merupakan satu collision domain.

![single collision domain](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/collision-domain-1.png "single collision domain")

Berbeda dengan hub, switch dan router memecah collision domain. Setiap port pada switch atau router berada pada collision domain yang terpisah.

![multiple collision domain](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/collision-domain-2.png "multiple collision domain")

Berikutnya **broadcast domain** pengertian singkatnya adalah suatu area dari jaringan di mana paket broadcast akan di teruskan (forwarded) ke setiap port yang berada pada broadcast domain yang sama. Router yang merupakan perangkat layer 3 akan memecah broadcast domain, karena setiap port pada router akan berada pada jaringan yang berbeda, sedangkan switch yang merupakan perangkat layer 2 tidak memecah broadcast domain, karena setiap port pada switch berada pada jaringan yang sama.

![broadcast-domain-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/broadcast-domain-1.png "broadcast-domain-1")

![broadcast-domain-2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/broadcast-domain-2.png "broadcast-domain-2")
 
## Tabel Routing
Supaya router dapat melakukan pemilihan jalur terbaik (routing), maka router harus memiliki paling tidak informasi berikut di dalam tabel routingnya:
- alamat network tujuan
- next-hop router / gateway

Seperti yang telah dijelaskan, informasi tersebut dapat dimasukkan secara manual oleh administrator atau didapatkan dari router yang lain menggunakan *dynamic routing protocol*. Selain itu, tabel routing juga berisi alamat network di mana router itu berada (*directly connected*).

![topologi-routing-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/topologi-routing-1.png "topologi-routing-1")

![tabel-routing-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/tabel-routing-1.png "tabel-routing-1")

Gambar di atas contoh dari tabel routing pada router Cisco yang memiliki ip address 192.168.1.1/24 pada interface fast ethernet 0/0 yang dimilikinya. Terlihat ada network 192.168.1.0/24 yang terdeteksi sebagai directly connected network pada interface FastEthernet0/0.

Beberapa catatan mengenai routing table sebuah router:
- Setiap router memilih jalur terbaik untuk dirinya sendiri, berdasarkan informasi yang dimiliki pada routing table-nya masing-masing.
- Informasi yang dimiliki sebuah router pada routing table-nya, belum tentu sama atau belum tentu ada pada routing table di router yang lain.
- Informasi routing yang menunjukkan sebuah jalur/rute dari satu network ke network yang lain, tidak berlaku untuk arah yang sebaliknya. Misal, jalur terbaik dari A ke B belum tentu sama dengan jalur dari B ke A. Perbedaan jalur pulang dan pergi seperti ini disebut dengan asymmetric routing.

## Static Routing
Static routing merupakan informasi route yang dimasukkan ke dalam tabel routing secara manual oleh administrator. Konfigurasi static route minimal harus ada alamat network tujuan dan alamat IP router atau gateway yang menjadi next-hop menuju network tujuannya.

![topologi-routing-statik](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/topologi-routing-statik.png "topologi-routing-statik")

![tabel-routing-2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/tabel-routing-2.png "tabel-routing-2")

Pada gambar di atas, terlihat contoh menambahkan static route pada R1, dimana untuk menuju network 192.168.2.0, R1 harus mem-forward traffic lewat R2 pada alamat IP 192.168.3.2.

Contoh konfigurasi static route pada router Cisco:

`ip route 192.168.2.0 255.255.255.0 192.168.3.2`

Static route yang telah kita tambahkan, akan muncul pada tabel routing seperti contoh di atas.

## Dynamic Routing
Static routing lebih mudah dan lebih praktis digunakan pada jaringan kecil. Akan tetapi pada jaringan besar, menggunakan static routing saja akan sangat merepotkan, karena setiap ada perubahan jaringan/route, maka administrator harus melakukan perubahan secara manual pada setiap router yang ada di jaringan. Untuk jaringan dengan 1 atau 2 router hal itu masih bisa dilakukan. Tetapi jika jumlah router sudah lebih dari 5, 10, atau bahkan ratusan router maka kita harus menggunakan dynamic routing.

Berbeda dengan static routing, pada dynamic routing kita cukup mengkonfigurasi network apa saja yang terhubung langsung oleh router tersebut (directly connected network). Selanjutnya setiap router akan melakukan komunikasi satu sama lain menggunakan dynamic routing protocol dan melakukan pertukaran informasi routing yang dimilikinya.

Jika ada perubahan pada network/route, maka administrator cukup melakukan perubahan/penambahan konfigurasi pada satu router saja yang terdapat perubahan tersebut. Router itu akan langsung menginformasikan perubahan yang terjadi ke router yang lain secara otomatis.

Ada beberapa protokol routing dinamis yang tersedia, contohnya adalah RIP, OSPF, EIGRP, IS-IS, dan BGP. Masing-masing protokol memiliki karakteristik dan metode tersendiri untuk memilih jalur terbaik. Secara umum, protokol routing dinamis dapat dibagi menjadi:

- Interior Gateway Protocol (IGP)
IGP digunakan di dalam jaringan yang berada di bawah satu Autonomous System (AS) yang sama. Atau dengan kata lain di bawah satu administrasi yang sama, misalnya jaringan internal suatu perusahaan. Contoh protokol routing IGP adalah RIP, OSPF, EIGRP dan IS-IS.
      
- Exterior Gateway Protocol (EGP)
EGP digunakan untuk melakukan routing antar AS atau antar jaringan yang berbeda administrasi. Misalnya routing antar Internet Service Provider (ISP), atau routing perusahaan yang memiliki blok IP address sendiri dengan Transit Providernya. Contoh protokol routing EGP adalah BGP.
Sekarang ini, selain sebagai EGP, protokol routing BGP juga banyak digunakan di jaringan internal seperti di data center, karena BGP merupakan protokol routing yang scalable dan sangat fleksibel untuk digunakan.

Ada 3 jenis cara kerja protokol routing dinamis saat ini:
- Distance Vector
Protokol routing yang termasuk distance vector akan melakukan pemilihan rute terbaik dengan membandingkan jarak antar rute-rute yang tersedia. Rute yang dipilih adalah rute yang memiliki jarak paling kecil. Contoh protokol routing distance vector adalah RIP.

    ![distance-vector](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/distance-vector.png "distance-vector")

	Pada contoh di atas jika menggunakan distance vector, router R1 untuk menuju network di belakang R5 akan menggunakan jalur melewati R4 sebagai rute terbaiknya. R1 melihat jumlah hop (hop count) melewati R4 akan lebih kecil daripada melewati R2 - R3.

    ```
    R1 --> R4 --> R5 = 2 hop count               --> terpilih sebagai best route
    R1 --> R2 --> R3 --> R5 = 3 hop count
    ```

- Link-state
Pada protokol routing link-state, router akan mengumpulkan semua informasi alamat network tujuan, next-hop router, beserta informasi link antar router pada satu domain jaringan sehingga masing-masing router akan mengetahui topologi lengkap dari suatu jaringan. Selanjutnya router akan melakukan kalkulasi penghitungan jalur yang paling pendek untuk setiap network dari informasi tersebut.

    ![link-state-routing](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/link-state-routing.png "link-state-routing")

    Pada contoh di atas jika menggunakan protokol routing link-state, router akan melakukan kalkulasi jalur terbaik untuk semua link berdasarkan cost. Cost yang dapat digunakan contohnya adalah besaran bandwidth pada tiap link. R1 memilih rute melewati R2 - R3 karena costnya lebih kecil dibandingkan jika melewati R4.

    Misal cost 1Gbps = 1 dan cost 100 Mbps = 10, maka
    ```
    R1 --> R2 --> R3 --> R5 = 3          --> terpilih sebagai best route
    R1 --> R4 --> R5 = 20
    ```

- Hybrid
Protokol yang menggunakan cara kerja hybrid akan menggabungkan beberapa aspek dari distance vector dan link-state routing protocol. Sebagai contoh, EIGRP menggunakan distance untuk memilih jalur terbaik, tetapi ditambah dengan penghitungan beberapa kriteria lain, seperti bandwidth, load, delay, reliability dan MTU.

## Default Route
Default route merupakan route yang digunakan router untuk mem-forward traffic jika tidak ada route lain yang lebih spesifik. Default route biasanya dikonfigurasi secara statik, tetapi bisa juga di advertise menggunakan dynamic routing.

Contoh penggunaan default route yang paling umum adalah untuk mengakses Internet. Kita tidak mungkin mengkonfigurasi seluruh alamat IP yang ada di Internet satu per satu secara manual, tetapi kita bisa mengkonfigurasi satu default route yang mengarah ke router ISP untuk bisa terhubung ke Internet.

Network address untuk default route diset sebagai 0.0.0.0/0 yang berarti mencakup seluruh alamat IP.

![topologi-default-route](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/topologi-default-route.png "topologi-default-route")

![tabel-routing-3](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/08/tabel-routing-3.png "tabel-routing-3")

Pada contoh di atas, router R1 terhubung ke Internet melalui interface FastEthernet0/1, maka untuk menambah route ke Internet, kita cukup menambahkan default route 0.0.0.0/0 menuju ip address router ISP sebagai next-hop nya.

## Metric & Administrative Distance
Di dalam menentukan jalur terbaik, router akan melihat yang namanya metric dan administrative distance.

**Metric** adalah suatu nilai yang digunakan oleh router untuk menentukan jalur mana yang lebih dipilih apabila ada lebih dari satu jalur untuk menuju network tujuan yang sama.

Beberapa contoh metric yang biasa digunakan oleh protokol routing:
- Hop count: menghitung berapa jumlah router/hop untuk menuju network tujuan.
- Bandwidth: jalur yang memilik bandwitdh yang besar akan lebih diprioritaskan.
- Load: router melihat utilisasi trafik pada suatu link untuk memilih jalur terbaik.
- Delay: router melihat berapa lama suatu paket bisa sampai di tujuan melewati jalur tertentu.
- Reliability: router melihat kehandalan suatu jalur dalam melewatkan trafik, misal apakah ada banyak error di jalur tersebut atau ada interface failure.
- Cost: suatu nilai yang ditentukan oleh router atau diset secara manual oleh administrator untuk menunjukkan nilai suatu jalur.

Ada protokol routing yang menggunakan salah satu metric saja, ada pula protokol routing yang menggunakan lebih dari satu metric.

**Administrative Distance (AD)** adalah nilai yang digunakan router untuk memilih suatu route apabila route tersebut didapat melalui lebih dari satu protokol routing. Nilai default AD sudah ditentukan oleh masing-masisng router, tetapi bisa dirubah secara manual oleh administrator. Nilai AD yang lebih kecil yang akan dipilih.

Berikut contoh nilai default AD yang digunakan oleh router Cisco:

| Sumber | Administrative Distance |
| ------ | ----------------------- |
| Directly connected | 0 |
| Static | 1 |
| EIGRP summary | 5 |
| External BGP | 20 |
| Internal EIGRP | 90 |
| IGRP | 100 |
| OSPF | 110 |
| IS-IS | 115 |
| RIP | 120 |
| EGP | 140 |
| ODR | 160 |
| External EIGRP | 170 |
| Internal BGP | 200 |
| Unknown | 255 (tidak akan dipakai) |

Singkatnya, apabila router mengetahui ada lebih dari satu informasi route yang mengarah ke network yang sama, maka router akan melihat:
- AD terlebih dahulu, yang lebih kecil yang dipilih
- Jika AD sama, router akan melihat metric, yang lebih kecil yang dipilih
- Jika AD dan metric sama, router dapat melakukan load balancing jalur-jalur tersebut