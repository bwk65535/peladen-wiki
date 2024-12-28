---
title: Bash Shell Expansion
description: 
published: true
date: 2024-12-28T10:47:45.642Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T10:47:45.642Z
---

Bash shell mempunyai kemampuan untuk melakukan *expansion* atau perluasan/pengembangan dari suatu *pattern* di command line. Contoh yang paling banyak dipakai adalah kemampuan **globbing** yang biasa di pakai untuk melakukan *matching* beberapa name file sekaligus.

## Pattern matching
Pattern matching atau disebut juga globbing adalah salah satu jenis shell expansion yang menggunakan *metacharacters* atau *wildcards characters* sebagai pengganti beberapa karakter nama path/file. Metacharacters tersebut akan di ganti (replace) dengan nama path/file yang asli sebelum suatu command dieksekusi.

Contoh *metacharacters* dan *pattern* yang digantikannya.

| Pattern | Matches |
| ------- | ------- |
| * | nol atau lebih karakter string |
| ? | satu karakter apapun |
| [abc...] | satu karakter apapun di dalam kurung siku |
| [!abc...] | satu karakter apapun yang **tidak** ada di dalam kurung siku |
| [^abc...] |  satu karakter apapun yang **tidak** ada di dalam kurung siku |
| [[:alpha:]] | semua karakter alfabet |
| [[:lower:]] | semua karakter lowercase |
| [[:upper]] | semua karakter uppercase |
| [[:alnum:]] | semua karakter alfabet atau digit |
| [[:punct:]] | semua *printable character* selain alfanumerik atau space |
| [[:digit:]] | satu karakter digit dari 0 sampai 9 |
| [[:space:]] | satu karakter *whitespace*, seperti tabs, newlines, carriage ceturn, form feed, atau space |

Contoh penggunaan pattern matching:
```
$ mkdir testglob; cd testglob
$ touch ayam angsa bebek cicak domba entok anoa burung cumi doplhin elang
$ ls
angsa  anoa  ayam  bebek  burung  cicak  cumi  domba  doplhin  elang  entok

# match di awali huruf a
$ ls a*
angsa  anoa  ayam

# match di awali huruf a atau c lalu selebihnya karakter apapun
$ ls [ac]*
angsa  anoa  ayam  cicak  cumi

# match yang tidak di awali huruf a atau c lalu selebihnya karakter apapun
$ ls [^ac]*
bebek  burung  domba  doplhin  elang  entok

# match 4 karakter apa saja
$ ls ????
anoa  ayam  cumi
```

## Tilde Expansion
Karakter tilde **~** menunjukkan path *home directory* dari user yang sedang aktif.
```
$ whoami
centos
$ echo ~
/home/centos

$ echo ~/testdir/
/home/centos/testdir/

$ ls ~
testdir

$ ls ~/testdir/
testfile1  testfile2  testfile3
```

## Brace Expansion
Brace expansion biasa digunakan untuk meng*generate* string sesuai pilihan karakter atau secara berurutan. Contoh praktisnya misalnya kita ingin membuat banyak file/folder sekaligus.
```
$ ls
testdir
$ mkdir folder{1..5}
$ ls
folder1  folder2  folder3  folder4  folder5  testdir

$ touch file{satu,dua,tiga}
$ ls
filedua  filesatu  filetiga  folder1  folder2  folder3  folder4  folder5  testdir
```
Brace expansion dapat juga digunakan secara nested
```
$ touch file{kambing,sapi}{1..3}.txt
$ ls
filekambing1.txt  filekambing2.txt  filekambing3.txt  filesapi1.txt  filesapi2.txt  filesapi3.txt
```

## Variable Expansion
*Variable* merupakan sesuatu yang digunakan untuk menyimpan *value*/data di memory. Variable memudahkan kita untuk menyimpan atau mengakses data yang akan digunakan ketka menjalankan command di shell.
```
# Assign value "google.com" ke variable "ALAMAT"
$ ALAMAT=google.com

# Contoh akses value dari variable
$ echo $ALAMAT
google.com

$ ping -c2 $ALAMAT
PING google.com (172.217.194.139) 56(84) bytes of data.
64 bytes from 172.217.194.139 (172.217.194.139): icmp_seq=1 ttl=105 time=28.8 ms
64 bytes from 172.217.194.139 (172.217.194.139): icmp_seq=2 ttl=105 time=27.2 ms

--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 655ms
rtt min/avg/max/mdev = 27.178/28.012/28.846/0.834 ms
```

Untuk membedakan dengan shell expansion yang lain, kita juga bisa menuliskan variable dengan menggunakan kurung kurawal.
```
$ IP_ADDRESS=192.168.56.1
$ echo ${IP_ADDRESS}
192.168.56.1
```

## Command Substitution
Command substitution digunakan apabila kita ingin menggunakan *output* dari suatu *command* di dalam *command* yang lain. Command substitution dapat digunakan dengan memasukkan command yang ingin diambil outputnya di dalam kurung lengkung yang diawali simbol dolar seperti `$(command)`. Command substitution dengan cara ini dapat dilakukan secara nested.
```
$ echo "hari ini adalah hari $(date +%A)"
hari ini adalah hari Saturday

$ echo "nama saya adalah $(whoami)"
nama saya adalah centos
```

Selain menggunakan cara `$(command)`, command substitution dapat dilakukan dengan menggunakan *backticks* (`).
Tetapi cara ini sudah mulai ditinggalkan karena simbol ini mudah tertukar dengan single quote(') dan tidak bisa dilakukan secara *nested*.
```
$ echo "path sekarang ada di `pwd`"
path sekarang ada di /home/centos/folder1
```

## Proteksi dari expansion
Kita dapat mencegah shell melakukan expansion dengan menggunakan backslash (\\), single quotes ('), atau double quotes (").

Backslash digunakan untuk mencegah satu karakter setelahnya terekspansi.
```
$ echo direktori user ini ada di $HOME
direktori user ini ada di /home/centos

$ echo direktori user ini ada di \$HOME
direktori user ini ada di $HOME
```

Double quotes digunakan untuk memproteksi dari globbing dan shell expansion, tetapi tetap memperbolehkan command dan variable substitution.
```
$ echo Home directory user ini ada di ~
Home directory user ini ada di /home/centos

$ echo "Home directory user ini ada di ~"
Home directory user ini ada di ~

$ RUMAH=/home/centos
$ echo "Home directory user ini ada di ${RUMAH}"
Home directory user ini ada di /home/centos
```

Single quotes digunakan untuk mencegah semua expansion dan substitution.
```
$ echo 'Home directory user ini ada di ~'
Home directory user ini ada di ~

$ echo 'Home directory user ini ada di $(RUMAH)'
Home directory user ini ada di $(RUMAH)

$ echo 'hostname mesin ini adalah $(hostname -s)'
hostname mesin ini adalah $(hostname -s)
```