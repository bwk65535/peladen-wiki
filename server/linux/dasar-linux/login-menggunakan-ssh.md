---
title: Login Menggunakan SSH
description: 
published: true
date: 2024-12-28T09:43:14.923Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T09:43:14.923Z
---

## Headless server
Umumnya server yang dijalankan untuk di data center tidak memiliki display monitor dan keyboard yang terhubung ke server tersebut. Untuk me-manage server, biasanya sysadmin akan terhubung melalui jaringan ke server tersebut dengan protokol SSH atau me-remote server tersebut via koneksi serial yang terhubung ke *console server* sebagai *out-of-band management*.

## Minimal Installation
Selain itu, seperti yang sudah diketahui, server linux biasanya tidak menginstall *graphical environment* atau GUI untuk menghemat penggunaan resource pada server. Server biasanya hanya diinstall software dan utilitas yang memang penting untuk digunakan, atau biasa disebut minimal install. Karena tidak ada GUI, mau tidak mau kita harus me-manage server tersebut melalui terminal yang bisa diakses secara remote.

## SSH Login
Di linux, metode remote login yang paling umum adalah menggunakan SSH. Singkatnya ssh merupakan protokol yang digunakan untuk mengoperasikan layanan jaringan secara terenkripsi, sehingga kerahasiaan dan integritas data yang melewati jaringan dapat dijamin.

Untuk terhubung ke server melalui ssh, cukup menggunakan command berikut:

`$ ssh user@alamat_server`

Ketika sudah terkoneksi, server akan meminta password user (jika ada) sebelum kita masuk ke server tersebut. Jika kita tidak menggunakan `user`, maka ssh akan menggunakan user kita yang sedang login. Untuk `alamat_server` dapat menggunakan ip address atau hostname asal hostname tersebut dapat di-*resolve* menjadi ip address.

## Public Key Authentication
Selain login dengan password, kita juga bisa login menggunakan *ssh keypair*. Beberapa penyedia public cloud mengharuskan kita untuk login ssh menggunakan metode ini. Selain itu, untuk kebutuhan scripting dan otomasi sering kali mengharuskan kita untuk bisa login ke suatu server tanpa harus mengetikkan password secara manual.

Kita harus membuat terlebih dahulu pasangan public dan private key yang akan digunakan.

`$ ssh-keygen -t rsa -b 4096`

Command tersebut digunakan untuk membuat RSA keypair dengan 4096 bit key. Kita bisa menggunakan metode enkripsi yang lain seperti ed25519 yang sekarang banyak digunakan. Contoh membuat ed25519 keypair dengan 100 key derivation function rounds:

`$ ssh-keygen -t ed25519 -a 100`

Setelahnya kita mendapat pilihan untuk menentukan lokasi untuk menyimpan keypair dan apakah ingin menggunakan *passphrase*. Jika ingin menggunakan keypair ini untuk kebutuhan passwordless login, maka tidak perlu mengisikan passphrase.

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/centos/.ssh/id_rsa): 
Created directory '/home/centos/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/centos/.ssh/id_rsa.
Your public key has been saved in /home/centos/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:j5HVe2FlVp0IsM2Y1VCy2Spb8qbi6cQismn0SsLquDU centos@localhost.localdomain
The key's randomart image is:
+---[RSA 4096]----+
|         ..==.. O|
|          B.=o =.|
|         +.=..o  |
|         o  .o . |
|        So o. .  |
|..    .  +*  .   |
|o+E. . o...o     |
|=o+o. o.. o      |
|**.   o+..       |
+----[SHA256]-----+
```

Untuk menggunakan ssh public authentication, public key yang sudah digenerate harus terlebih dahulu dimasukan ke file `authorized_keys` pada server tujuan. Proses copy dapat dilakukan secara manual atau menggunakan tool `ssh-copy-id`.

```
$ ssh-copy-id centos@192.168.121.65
/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/centos/.ssh/id_rsa.pub"
/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
centos@192.168.121.65's password: 
```
Setelah ini, kita dapat melakukan ssh tanpa login kembali. Bisa dengan command ssh seperti biasa, atau ditambah opsi `-i` untuk menggunakan private key tertentu yang ingin kita gunakan. Dengan catatan, opsi *PubkeyAuthentication* di-enable di ssh server.

`$ ssh -i .ssh/id_rsa centos@192.168.121.65`

## SSH Known Host
Ketika pertama kali kita terhubung ke ssh server, mesin yang menjadi ssh server akan mengirimkan public ssh host key yang dimilikinya ke mesin ssh client. Host key ini berfungsi untuk authentikasi dan untuk membangun koneksi terenkripsi. Sama seperti ssh key yang kita gunakan untuk pubkey authentication, host key ini juga berupa keypair public dan private key. 

Public key yang dikirim ke client akan dimasukkan ke file `known_host` di sisi client seperti contoh di bawah:

`$ cat ~/.ssh/known_host`
```
192.168.121.65 ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCcWrZ0rhXAI0j1c5SjSlJA8KQFNzSP1nipqGuW4pvGbU2VC+tV1hakXR5zLeQ7h8/pBqinNxqrdYbQBgQwojxc=
```

Jika pasangan ip dan publik key tersebut berbeda dengan public key yang diberikan server, maka akan muncul peringatan seperti berikut:

```@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:IkmL2524Vhv40I5HEagQ3mAv/79Mza8F6HatcPznK0A.
Please contact your system administrator.
Add correct host key in /home/centos/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/centos/.ssh/known_hosts:1
ECDSA host key for 192.168.121.65 has changed and you have requested strict checking.
Host key verification failed.
```

Jika kita yakin server tersebut benar yang kita akses, atau malah kita sendiri yang mengganti ssh host key pada server, maka cukup hapus baris ip address + publik key di file known_host atau bisa dengan command di bawah ini:

```
$ ssh-keygen -f "/home/centos/.ssh/known_hosts" -R "192.168.121.65"
# Host 192.168.121.65 found: line 1
/home/centos/.ssh/known_hosts updated.
Original contents retained as /home/centos/.ssh/known_hosts.old
```

Selanjutnya jika kita coba akses kembali, maka server akan mengirimkan ssh key fingerprint yang baru.

```
$ ssh -i .ssh/id_rsa centos@192.168.121.65
The authenticity of host '192.168.121.65 (192.168.121.65)' can't be established.
ECDSA key fingerprint is SHA256:jThXIolnLqKLMCszfKTRgnDTkHxLdmPFcn0E/FDRkFw.
ECDSA key fingerprint is MD5:6d:fa:25:58:17:86:6c:03:99:09:17:52:d6:47:ff:84.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.121.65' (ECDSA) to the list of known hosts.
```