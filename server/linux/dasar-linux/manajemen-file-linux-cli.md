---
title: Manajemen file di Linux menggunakan CLI
description: 
published: true
date: 2024-12-28T10:38:58.470Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T10:38:58.470Z
---

CLI pada linux menyediakan beberapa perintah dasar yang dapat digunakan untuk mengelola file/folder.

Berikut di antaranya:

| Command | Fungsi |
| ------- | ------ |
| mkdir | membuat direktori baru |
| cp | kopi file |
| cp -r | kopi folder beserta isi |
| mv | memindahkan atau merubah nama file/folder |
| rm | menghapus file |
| rm -r | menghapus folder beserta isi |
| rmdir | menghapus folder kosong |

Berikut beberapa contoh penggunaan:

## mkdir
```
$ mkdir dir1
$ ls
dir1
$ mkdir dir2 dir3
$ ls
dir1  dir2  dir3
```

## cp
```
$ ls
dir1  dir2  dir3  file1
$ cp file1 file2
$ ls
dir1  dir2  dir3  file1  file2
$ cp file1 dir3/file3
$ ls dir3
file3
```

## cp -r
```
$ ls dir3
file3
$ cp -r dir3 dir4
$ ls dir4
file3
$ ls dir3
file3
```

## mv
```
$ ls
dir1  dir2  dir3  dir4  file1  file2
$ mv file2 dir2/
$ ls
dir1  dir2  dir3  dir4  file1
$ ls dir2
file2
```

## rm
```
$ ls
dir1  dir2  dir3  dir4  file1
$ rm file1
$ ls
dir1  dir2  dir3  dir4
```

## rm -r
```
$ ls
dir1  dir2  dir3  dir4
$ rm -r dir4
$ ls
dir1  dir2  dir3
$ rm -r dir2 dir3
$ ls
dir1
```

## rmdir
```
$ ls
dir1
$ ls dir1
$ rmdir dir1
$ ls

```