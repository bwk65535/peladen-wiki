---
title: Link antar file di Linux
description: 
published: true
date: 2024-12-28T10:41:29.847Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T10:41:29.847Z
---

Pada sistem operasi linux, kita dapat membuat **link** atau suatu nama yang mereferensikan/menunjuk ke suatu file/direktori.

Ada 2 macam link di linux:
## Hard link
File yang dibuat di linux mempunyai sebuah hard link yang menunjuk ke data pada file system. Jika kita membuat hard link baru untuk file tersebut, sama saja dengan membuat nama baru yang menunjuk ke data yang sama. Tidak terlihat bedanya link/nama yang pertama digunakan atau link yang baru tersebut.

Karena hard link menunjuk ke data yang sama, maka setiap hard link juga akan mempunyai *permission*, *ownership*, *timestamp*, dan *link count* yang sama.

Kita bisa melihat jumlah link pada suatu file dengan command `ls -l`.
```
$ touch file1
$ ls -l
-rw-rw-r--. 1 vagrant vagrant 0 Apr 18 04:44 file1
```
Angka 1 di sebelah kiri user menunjukkan jumlah hard link file tersebut.

Untuk membuat hard link, cukup menggunakan *command* `ln path_to_source path_to_link` seperti berikut:
```
$ ln file1 file2
$ ls -l
-rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file1
-rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file2
```
Dari contoh di atas, terlihat setelah dibuat hard link baru, jumlah hard link menjadi 2.

Kita juga bisa melihat bahwa hard link tersebut menunjuk ke *inode* file yang sama dengan opsi `-i`.
Kolom paling kiri menunjukkan nilai *inode* yang sama yaitu 201853973.
```
$ ls -li
201853973 -rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file1
201853973 -rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file2
```

### Limitasi hard link
- Hanya bisa untuk *regular file*, tidak bisa untuk direktori maupun *special file*.
- Hanya bisa digunakan jika *source* dan *target link* berada pada *file system* yang sama.

## Soft link
Berbeda dengan hard link, soft link atau *symbolic link* hanya berfungsi untuk pointing ke file aslinya. Mirip seperti *shortcut* di sistem operasi Windows. Soft link merupakan salah satu special file di linux.

Soft link mempunyai beberapa keunggulan di bandingkan dengan hard link:
- Dapat digunakan untuk menghubungkan file di file system yang berbeda.
- Dapat membuat link ke direktori ataupun file.

Kita bisa membuat soft link dengan command `ln -s path_to_source path_to_link`
```
$ ln -s file1 file3
$ ls -l
-rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file1
-rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file2
lrwxrwxrwx. 1 vagrant vagrant 5 Apr 18 06:56 file3 -> file1
```
Pada contoh di atas terlihat file3 merupakan soft link ke arah file1. Terlihat juga tipe filenya `l` pada output `ls -l` paling kiri.

Ciri lain dari soft link apabila file sumbernya dihapus, soft link akan tetap ada tetapi tidak bisa diakses karena file sumbernya sudah tidak ada.
```
$ ls -l
total 0
-rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file1
-rw-rw-r--. 2 vagrant vagrant 0 Apr 18 04:44 file2
lrwxrwxrwx. 1 vagrant vagrant 5 Apr 18 06:56 file3 -> file1
$ rm file1
$ ls -l
total 0
-rw-rw-r--. 1 vagrant vagrant 0 Apr 18 04:44 file2
lrwxrwxrwx. 1 vagrant vagrant 5 Apr 18 06:56 file3 -> file1
$ cat file3
cat: file3: No such file or directory
```

Contoh lain membuat soft link ke direktori:
```
[vagrant@centos8 ~]$ mkdir testdir
[vagrant@centos8 ~]$ touch testdir/file4
[vagrant@centos8 ~]$ ln -s testdir linkdir
[vagrant@centos8 ~]$ ls -l
total 0
-rw-rw-r--. 1 vagrant vagrant  0 Apr 18 04:44 file2
lrwxrwxrwx. 1 vagrant vagrant  7 Apr 18 07:08 linkdir -> testdir
drwxrwxr-x. 2 vagrant vagrant 19 Apr 18 07:07 testdir
[vagrant@centos8 ~]$ ls testdir/
file4
[vagrant@centos8 ~]$ ls linkdir/
file4
```