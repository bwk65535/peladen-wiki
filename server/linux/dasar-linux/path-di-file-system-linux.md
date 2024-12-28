---
title: Path di File System Linux
description: 
published: true
date: 2024-12-28T10:33:48.107Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T10:33:48.106Z
---

**Path** merupakan suatu alamat yang menunjukkan lokasi dari file atau direktori di dalam file system. Path bersifat unik dan menggunakan tanda **/** (forward slash) sebagai separator antar direktori. Suatu direktori/*folder* dapat direferensikan dengan cara yang sama seperti file di dalam path.

## Pembagian path
Path dibagi menjadi 2, yaitu:
1. Absolute Path
   Absolute path merupakan alamat lengkap (*fully qualified*) dari suatu file atau direktori di dalam file system. Absolute path dimulai dari "/" (*root directory*) hingga lokasi spesifik file/direktori tersebut.

   Sebagai contoh file surat.txt yang berada di dalam direktori arsip didalam *home folder* user1 dapat ditulis menggunakan absolute path sebagai `/home/user1/arsip/surat.txt`.

2. Relative Path
   Untuk dapat menggunakan relative path, kita harus mengetahui terlebih dahulu *current working directory* atau direktori tempat kita berada. Relative path merupakan lokasi suatu file/direktori relatif dari *current working directory* (CWD).

   CWD dapat diketahui menggunakan command **pwd**
   ```
   $ pwd
   /home/user1
   ```
   Terlihat dari contoh di atas, kita berada di /home/user1. Jika ingin menuju file surat.txt seperti pada contoh absolute path di atas, kita cukup menggunakan alamat `arsip/surat.txt`.

## Navigasi path
Perbedaan absolute path dan relative path terlihat dari kelengkapan alamatnya. Absolute path selalui dimulai dari root directory "/", sedangkan relative path tidak demikian.

Di dalam relative path ada simbol titik "**.**" yang menunjukkan *current working directory* dan simbol dua titik "**..**" yang menunjukkan direktori satu tingkat sebelumnya. Kedua simbol ini dapat memudahkan navigasi menggunakan relative path.

Berikut contoh-contoh navigasi path:
- membuat file /home/user1/arsip/surat.txt
  - absolute
  ```
  $ mkdir /home/user1/arsip
  $ touch /home/user1/arsip/surat.txt
  $ ls -l /home/user1/arsip/
  -rw-rw-r--. 1 user1 user1 0 Jan 31 01:45 surat.txt
  ```
  - relative
  ```
  $ pwd
  /home/user1
  $ mkdir arsip
  $ touch arsip/surat.txt
  $ ls -l arsip
  -rw-rw-r--. 1 user1 user1 0 Jan 31 01:48 surat.txt
  ```
- melihat file /etc/resolv.conf
  - absolute
  ```
  $ cat /etc/resolv.conf 
  nameserver 1.1.1.1
  nameserver 1.0.0.1
  ```
  - relative
  ```
  $ pwd
  /var
  $ cat ../etc/resolv.conf 
  nameserver 1.1.1.1
  nameserver 1.0.0.121.1
  ```