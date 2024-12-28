---
title: Instalasi GNS3 VM di Virtualbox
description: 
published: true
date: 2024-12-28T11:02:21.646Z
tags: virtualbox, labs
editor: markdown
dateCreated: 2024-12-28T11:02:21.646Z
---

[GNS3](https://www.gns3.com/) merupakan salah satu software simulasi jaringan yang banyak dipakai saat ini. Di dalamnya kita bisa menjalankan beberapa perangkat jaringan real seperti router, switch, firewall, server ataupun perangkat lainnya menggunakan teknologi virtualisasi. Kita bisa menggunakan software virtualisasi yang umum digunakan seperti virtualbox, qemu, dan dynamips.

Saat ini, GNS3 juga memiliki versi virtual machine (GNS3 VM) yang dapat langsung kita jalankan di virtualbox. Dari laptop/PC kita bisa mengakses GNS3 di VM tersebut melalui web UI. Fitur ini sama seperti yang telah ada pada software simulasi lainnya seperti [EVE-NG](https://www.eve-ng.net/) .

## Prasyarat
- Niat
- PC/Laptop yang sudah enable virtualization (Intel VT-x / AMD-V).
- Virtualbox/VMWare Workstation yang sudah terinstall.

## Step 1 - Download GNS3 VM
- Pada halaman download GNS3, pilih GNS3 VM.
![download gns3](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/download_gns3vm_01.png "download gns3")

- Lalu pilih image sesuai virtualisasi yang digunakan. Disini saya pilih image Virtualbox.
![download gns3 vbox](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/download_gns3vm_02.png "download gns3 vbox")

- Jika sudah di download, buka Virtualbox lalu klik File > Import Appliance
![vbox import appliance](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/vbox_import.png "vbox import appliance")

- Pilih image GNS3 yang sudah di download, lalu ubah beberapa konfigurasi seperti berikut. Sesuaikan jumlah resource CPU dan Memory yang bisa digunakan oleh komputer anda.
![vbox import gns3 vm](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/vbox_import2.png "vbox import gns3 vm")

- Selanjutnya GNS3 VM akan muncul di Virtualbox. Klik start untuk menyalakan. Jika sudah, akan muncul informasi GNS3 VM yang sedang berjalan.
![gns3 start](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/gns3vm_start.png "gns3 start")

## Step 2 - Konfigurasi statik IP
By default, sudah terdapat konfigurasi 2 network adapter di GNS3 VM. Adapter pertama terhubung ke Host-only network pada Virtualbox dan adapter kedua terhubung ke NAT Network. Kedua adapter tersebut mengaktifkan DHCP sehingga seharusnya kita bisa langsung terhubung ke VM menggunakan ip Host-only adapter, dan VM tersebut dapat mengakses Internet melalui NAT network.
![gns3 network](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/gns3vm_net.png "gns3 network")

Untuk lebih nyamannya, kita bisa mengkonfigurasi ip address statik pada host-only adapter sehingga ip GNS3 VM tersebut tidak berubah-ubah.

- Pertama, klik OK pada jendela awal GNS3.
- Akan muncul menu seperti berikut. Klik network. Klik Yes apabila ada notifikasi server akan reboot setelah ini.<br>
![gns3 network setting](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/gns3vm_netsetting.png "gns3 network setting")

- Selanjutnya akan muncul jendela nano editor yang sudah membuka file `/etc/netplan/90-gns3vm-static-netcfg.yaml`. *Uncomment* konfigurasi berikut serta isi ip address sesuai keinginan. Jangan lupa sesuaikan juga nama network adapter di dalam VM. Di VM saya adapter pertama menggunakan nama enp0s3 dan adapter kedua menggunakan nama enp2s8. Jika tidak yakin, cek terlebih dahulu nama adapter menggunakan shell.
![gns3 set static ip](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/gns3vm_static_ip.png "gns3 set static ip")<br>
Note: by default host-only adapter pertama di virtualbox akan menggunakan network address 192.168.56.0/24.

- Jika sudah, simpan file di nano menggunakan `Ctrl+o`, dan exit nano menggunakan `Ctrl+x`. Server akan melakukan reboot dan akan memakai ip yang sudah disetting statik sebagai ip host-only adapter.

## Step 3 - Akses GNS3 VM Web UI
Kita bisa mengakses web ui menggunakan browser dengan memasukkan alamat ip GNS3 VM yang sudah kita setting sebelumnya.
![gns3 webui](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/gns3_webui.png "gns3 set webui")

## Kesimpulan
Penggunaan GNS3 VM akan memudahkan kita dalam menggunakan GNS3 karena step-step penginstalannya akan sama di platform manapun karena menggunakan virtualisasi. Selain itu, image atau virtual disk dari GNS3 VM tersebut dapat kita backup lalu pindahkan kemana saja dan data di dalamnya tidak hilang semisal jika kita melakukan install ulang OS ataupun mengganti komputer/laptop.