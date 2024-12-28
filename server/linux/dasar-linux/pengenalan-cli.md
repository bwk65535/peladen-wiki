---
title: Pengenalan Command Line Interface (CLI)
description: 
published: true
date: 2024-12-28T08:23:48.148Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T08:23:48.148Z
---

Di dalam menggunakan komputer, kita sebagai pengguna (user) membutuhkan antar muka (interface) untuk memberikan perintah atau instruksi ke dalam komputer. Bentuk interface yang banyak digunakan untuk mengkonfigurasi sebuah server adalah CLI (Command Line Interface) yang berbentuk teks (text based).
Walaupun ada juga interface berbentuk grafis (Graphical User Interface), interface berbentuk CLI lebih banyak digunakan di server karena beberapa alasan berikut:
- hemat resource, karena hanya menampilkan teks saja tidak ada gambar.
- meminimalkan aplikasi yang harus diinstal di server
- meminimalkan celah keamanan pada server yang mungkin ada pada interface grafis.
- dll.

## Pengenalan Shell
CLI di linux disediakan oleh program atau command line interpreter yang bernama *shell*. Ada banyak shell yang dikembangkan dan digunakan pada sistem linux. Salah satu shell yang saat ini banyak digunakan adalah Bourne-Again Shell (Bash Shell) yang merupakan pengembangan dari Bourne Shell (sh)

Ketika kita memulai shell, maka akan muncul prompt seperti berikut:

`[centos@machine1 ~]$`

Secara default, format tampilannya adalah `[username@hostname current_directory]$`. Tanda `~` merupakan home directory dari user tersebut. Pada contoh diatas, username-nya adalah centos, hostname-nya machine1, dan current directory-nya ~ atau /home/centos.

Tanda `$` merupakan *shell prompt*, tempat di mana perintah atau command dapat kita inputkan secara interaktif. Prompt "$" merupakan prompt untuk regular user, sedangkan prompt "#" merupakan prompt untuk root user.

`[root@machine1 ~]#`

Selain dapat digunakan secara interaktif, kita juga dapat menjalankan command menggunakan script yang dijalankan oleh shell tersebut. Hal ini berguna untuk mengotomasi instalasi atau konfigurasi pada server. Selain itu, shell scripting juga dapat digunakan untuk memonitor server dan alerting.

## Menggunakan shell
Suatu perintah atau *Command* yang dijalankan pada shell biasanya terdiri dari 3 bagian:
- Command: perintah atau program yang akan dijalankan.
- Options: opsi-opsi untuk merubah *behaviour* suatu command.
- Arguments: sesuatu yang merupakan target dari command tersebut.

Contoh:

`[centos@machine1 ~]$ ls -l /home`
- command `ls` untuk list isi direktori
- option `-l` untuk menampilkan output secara long listing
- argument `/home` sebagai direktori target yang akan di-list

## Console
Tempat kita menjalankan shell disebut dengan *terminal*. Untuk mengakses terminal, kita dapat menggunakan *physical console* atau *virtual console*.

Physical console dapat digunakan dengan display monitor dan keyboard yang dicolok langsung ke server. Atau dapat juga melalui serial port server tersebut yang diakses dari perangkat lain.

Di dalam physical console pada sistem linux, terdapat beberapa virtual console yang menjalankan terminal terpisah di dalamnya. Jika kita login di salah satu virtual console tersebut, itu merupakan sesi terminal sendiri dan terpisah dengan sesi terminal di virtual console yang lain.

Virtual console dapat diakses dengan shortcut Ctrl+Alt+F1 sampai F6. Secara default pada Centos 8, login screen untuk *graphical environment* (jika ada) dapat diakses di virtual console pertama. Jika sudah login, *graphical session* kita akan dijalankan di virtual console berikutnya yang masih kosong. Sedangkan virtual console pertama tetap menjalankan login screen. Ini berbeda pada Centos 6 dan 7 dimana graphical session kita akan tetap berada di virtual console pertama.
