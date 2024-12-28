---
title: Beberapa perintah dasar di Linux
description: 
published: true
date: 2024-12-28T09:47:27.607Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T09:47:27.607Z
---

Seperti yang telah diketahui sebelumnya, bash shell merupakan shell yang paling banyak dipakai di berbagai linux distro. Berikut beberapa contoh perintah (command) dasar pada bash shell yang biasa dipakai oleh sysadmin sehari-hari.

## whoami
Command untuk melihat user yang sedang dipakai (aktif)
```
$ whoami
centos
```

## date
Command untuk melihat tanggal dan jam. Command date support beberapa jenis formatting.
```
$ date
Tue Dec  1 04:12:23 UTC 2020
```
Melihat tanggal dalam waktu UTC and format ISO 8601
```
$ date -u +"%Y-%m-%dT%H:%M:%S%Z"
2020-12-01T04:13:54UTC
```
Menampilkan tanggal dalam format RFC-3339
```
$ date --rfc-3339=s
2020-12-01 04:14:13+00:00
```
Menampilkan tanggal dalam format *seconds since epoch* (1970-01-01 00:00:00 UTC)
```
$ date +%s
1606796337
```
Menampilkan tanggal 45 hari kemudian
```
$ date -d "+45 days"
Fri Jan 15 04:20:54 UTC 2021
```
dan masih banyak lagi contoh pemformatan `date` yang dapat dilihat pada `man date`.

## passwd
mengubah password user. Jika tanpa menulis usernya, maka akan merubah password user itu sendiri.
```
$ passwd
Changing password for user centos.
Current password: 
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.
```
Jika login sebagai root, kita bisa merubah password user lain.
```
# passwd centos
Changing password for user centos.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.
```

## file
di linux, ekstensi file tidak menunjukkan tipe file itu sendiri, sehingga ekstensi file seringkali tidak digunakan. Untuk mengetahui jenis/tipe suatu file digunakan command `file` seperti berikut:
```
$ file testfile 
testfile: ASCII text

$ file /usr/bin/ls
/usr/bin/ls: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=937708964f0f7e3673465d7749d6cf6a2601dea2, stripped, too many notes (256)

$ file /bin
/bin: symbolic link to usr/bin
```

## cat
melihat isi file
```
$ cat testfile 
satu
dua
tiga
empat

$ cat .bashrc 
# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi

# User specific environment
PATH="$HOME/.local/bin:$HOME/bin:$PATH"
export PATH
...output dipotong...
```

## head
melihat beberapa baris dari awal file, defaultnya 10 baris pertama.
```
$ head /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
```
dapat juga kita spesifikan dengan opsi `-n` berapa baris pertama yang ingin ditampilkan.
```
$ head -n 5 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```

## tail
melihat beberapa baris dari akhir file, defaultnya 10 baris terakhir.
```
$ tail /etc/passwd
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
polkitd:x:998:996:User for polkitd:/:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
unbound:x:997:994:Unbound DNS resolver:/etc/unbound:/sbin/nologin
sssd:x:996:992:User for sssd:/:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
chrony:x:995:991::/var/lib/chrony:/sbin/nologin
vagrant:x:1000:1000::/home/vagrant:/bin/bash
centos:x:1001:1001::/home/centos:/bin/bash
```
dapat juga kita spesifikan dengan opsi `-n` berapa baris terakhir yang ingin ditampilkan.
```
$ tail -n 5 /etc/passwd
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
chrony:x:995:991::/var/lib/chrony:/sbin/nologin
vagrant:x:1000:1000::/home/vagrant:/bin/bash
centos:x:1001:1001::/home/centos:/bin/bash
```

## wc
command untuk menghitung jumlah baris, kata, dan karakter dalam suatu file atau bisa juga dari input pipe.
```
$ wc /etc/passwd
  26   54 1286 /etc/passwd
```
- 26: baris
- 54: kata
- 1286: karakter

wc juga mempunyai opsi-opsi untuk menampilkan salah satu hal saja:
```
$ wc -l /etc/passwd
26 /etc/passwd
$ wc -w /etc/passwd
54 /etc/passwd
$ wc -c /etc/passwd
1286 /etc/passwd
```

## history
melihat riwayat command yang sudah pernah kita jalankan.
```
$ history
    1  whoami
    2  date
    3  date +"%c"
    4  date -u +"%Y-%m-%dT%H:%M:%S%Z"
    5  date --rfc-3339=s
    6  date +%s
    7  date -d "+45 days"
    8  passwd
    9  exit
   ...output dipotong...
```
tanda seru "!" dapat digunakan untuk menjalankan kembali command yang ada di history, contoh:
- !*string* akan menjalankan command *string* terakhir yang ada di history.
```
   ...output dipotong...
   14  file /etc/passwd
   15  file /etc/printcap 
   16  ls 
   17  ls -l /
   18  file /bin
   19  cat testfile 
   20  cat /var/log/secure 
   21  cat testfile 
   22  exxit
   23  exit
   24  cat .bashrc 
   25  history
[centos@localhost ~]$ !file
file /bin
/bin: symbolic link to usr/bin
```
- !*number* akan menjalankan command sesuai nomor urutan yang ada di history.
```
   ...output dipotong...
   13  file /usr/bin/ls
   14  file /etc/passwd
   15  file /etc/printcap 
   16  ls 
   17  ls -l /
   18  file /bin
   19  cat testfile 
   20  cat /var/log/secure 
   21  cat testfile 
   22  exxit
   23  exit
   24  cat .bashrc 
   25  history
   26  file /bin
   27  history
   28  ls -l /
   29  history
[centos@localhost ~]$ !15
file /etc/printcap 
/etc/printcap: ASCII text
```

## backslash \
karakter backslash `\` digunakan untuk memotong baris command yang panjang sehingga dapat berlanjut di line berikutnya, tetapi tetap dihitung sebagai satu baris. Biasanya digunakan supaya command yang panjang lebih mudah dibaca.
```
$ head -n 2 \
> /etc/passwd \
> /etc/group \
> /etc/hosts
==> /etc/passwd <==
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin

==> /etc/group <==
root:x:0:
bin:x:1:

==> /etc/hosts <==
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
```

## command line shortcut
ketika kita mengetik di terminal/cli, ada beberapa shortcut yang dapat digunakan untuk memindahkan kursor secara cepat.

| shortcut | deskripsi |
| -------- | --------- |
| Ctrl+A   | pindah ke awal baris |
| Ctrl+E   | pindah ke akhir baris |
| Ctrl+U   | hapus dari kursor sampai awal |
| Ctrl+K   | hapus dari kursor sampai akhir |
| Ctrl+panah kiri | pindah ke awal kata sebelumnya |
| Ctrl+panah kanan | pindah ke awal kata sesudahnya |
| Ctrl+R   | mencari history command dengan *pattern* |
