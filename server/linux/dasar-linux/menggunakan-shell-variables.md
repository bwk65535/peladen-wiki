---
title: Menggunakan Shell Variables
description: 
published: true
date: 2025-01-01T14:47:37.159Z
tags: server, linux
editor: markdown
dateCreated: 2025-01-01T14:47:37.159Z
---

Program shell seperti bash memungkinkan kita mengeset *shell variables* untuk mengubah behaviour dari shell. Selain itu, *shell variables* dapat kita export mejadi *environment variables* sehingga bisa digunakan oleh program yang kita jalankan di shell tersebut.

*Shell variables* hanya berlaku di shell yang sedang berjalan (current shell session). Jika kita membuka shell baru, maka *shell variables* yang kita buat di shell sebelumnya tidak terbawa di shell session yang baru.

## Mengeset value ke variable
Kita bisa mengeset variable beserta valuenya menggunakan sintaks berikut:
`NAMA_VARIABLE=value`

Nama variable dapat berupa uppercase, lowercase, angka, atau karakter undescore:
```
$ ANGKA=99
$ ikan=Gurame
$ ACCESS_ID=jf98j892gh894
$ temp1=/var/tmp
```

Kita bisa mengecek shell variables yang sudah diset di current shell menggunakan command *set*:
```
$ set | grep -iE 'angka|ikan|access|temp'
ACCESS_ID=jf98j892gh894
ANGKA=99
ikan=Gurame
temp1=/var/tmp
```

Seperti yang sudah disebutkan sebelumnya, shell variables yang kita set hanya berlaku di shell tempat kita mengeset variables tersebut (current shell).

## Mengambil value dari variable
Kita bisa mengambil value dari variable menggunakan *shell expansion*. Cukup menambah simbol dollar "$" di depan nama variable:
```
$ echo $ANGKA
99
$ echo $ikan
Gurame
$ echo $ACCESS_ID
jf98j892gh894
$ echo $temp1
/var/tmp
```

Jika ada karakter yang ingin kita masukkan berdekatan dengan nama variable, supaya tidak membingungkan masukkan nama variable tersebut di dalam kurung kurawal `{}` seperti berikut:
```
$ HARGA=1000
$ echo Rp${HARGA}
Rp1000

$ BERAT=50
$ echo ${BERAT}Kg
50Kg
```

## Mengubah shell behaviour menggunakan shell variable
Shell variable juga dapat digunakan untuk mengubah konfigurasi shell program seperti Bash. Sebagai contoh, kita dapat mengubah shell prompt dengan mengeset shell variable `PS1`:
```
$ PS1="\u@\H di \w jam \t$ "
redhat@rhel8-vm di ~ jam 12:39:05$
redhat@rhel8-vm di ~ jam 12:42:42$ cd /var/tmp
redhat@rhel8-vm di /var/tmp jam 12:42:45$
```

Contoh di atas akan menampilkan shell prompt dengan format berikut:
- \u: user yang sedang login
- \H: nama host lengkap
- \w: current directory
- \t: jam dengan format 24-hour HH:MM:SS

Untuk mengetahui lebih lengkap shell variables apa saja yang dapat kita set di bash, bisa kita lihat di manual bash `man 1 bash`.
