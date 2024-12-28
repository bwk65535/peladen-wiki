---
title: Hierarki File System Linux
description: 
published: true
date: 2024-12-28T09:49:55.489Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T09:49:55.489Z
---

Semua file di linux disusun di dalam file system secara hierarkis. Bentuk hierarkinya berupa *inverted tree*, yaitu *root* berada di puncak hierarki. Di bawah root terletak beberapa direktori dan subdirektori yang saling berurutan.

```
/
├── bin -> usr/bin
├── boot
├── dev
├── etc
├── home
│   ├── user1
│   └── user2
├── lib -> usr/lib
├── lib64 -> usr/lib64
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/sbin
├── srv
├── sys
├── tmp
├── usr
│   ├── bin
│   ├── lib
│   ├── lib64
│   ├── sbin
│   └── tmp -> ../var/tmp
└── var
    └── tmp
```

direktory **/** merupakan root directory atau direktori paling atas di sebuah file system. Setiap direktori yang ada di bawah root *by default* mempunyai kegunaannya masing-masing. Misalnya direktori **boot** untuk menyimpan file yang dibutuhkan untuk *booting system*, direktori **etc** untuk menyimpan file konfigurasi, dll.

Selain menunjukkan root, tanda **/** juga digunakan sebagai pemisah direktori untuk menunjukkan *path*. Misalnya subdirektori Documents ada di dalam direktori home, maka bisa ditulis menjadi */home/Documents*. 

Berikut beberapa contoh direktori penting di Linux:

| Direktori | Fungsi/isi direktori |
| --------- | -------------------- |
| /boot | file-file yang dibutuhkan untuk proses *booting* |
| /dev | *special device file* yang digunakan sistem untuk mengakses hardware |
| /etc | file konfigurasi sistem serta konfigurasi aplikasi |
| /home | data dan file user yang menggunakan sistem |
| /root | direktori home khusus untuk user root |
| /run | berisi *runtime data* yang digunakan oleh proses yang sedang berjalan |
| /tmp | *temporary files* yang dihapus otomatis dalam interval tertentu |
| /usr | berisi software, library, dan file-file yang digunakan program |
| /var | file-file dinamis yang digunakan sistem, tetapi persistent |

Konten/file yang ada di dalam direktori-direktori tersebut dibagi menjadi 4 jenis:
- static, isi file tidak berubah kecuali di *edit* secara manual/eksplisit.
- dynamic/variable, isi file bisa dimodifikasi oleh proses.
- persistent, isi file tetap ada setelah reboot.
- runtime, file yang digunakan oleh proses dan dihapus ketika reboot.