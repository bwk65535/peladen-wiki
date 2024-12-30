---
title: Membuat Ansible Inventory
description: 
published: true
date: 2024-12-30T12:26:27.607Z
tags: automation, ansible
editor: markdown
dateCreated: 2024-12-30T12:26:27.607Z
---

Ansible inventory merupakan suatu file yang berisi alamat host/alamat ip dari client yang akan dimanage. Beberapa alamat host dapat dibuat menjadi grup dan dimanage secara bersamaan. Suatu grup dapat berisi group lain dibawahnya dan suatu host dapat dimasukkan ke lebih dari satu grup. Di dalam inventory file kita juga dapat mengeset variable yang digunakan oleh host atau grup.

Alamat host di dalam inventory dapat didefinisikan secara statik atau dinamik. Alamat host statik dapat langsung ditulis di dalam inventory file, sedangkan alamat host dinamik dapat digenerate menggunakan script. Script ini yang akan mengambil data dari luar dan menyimpannya ke dalam inventory file.

## Lokasi inventory file
Ansible mempuyai default inventory file yang berada di `/etc/ansible/hosts`. Ansible akan menggunakan file inventory tersebut apabila kita menjalankan command ansible atau ansible-playbook tanpa menyebutkan lokasi inventory file secara spesifik.

Kita bisa menentukan inventory file yang akan dipakai menggunakan parameter command `--inventory path_to_file` atau `-i path_to_file`.

## Static Inventory
Inventory statik merupakan sebuah teks file dalam format INI-style atau YAML yang berisi alamat host yang dimanage ansible. Selain kedua format tersebut ansible men-support beberapa format file lain, tetapi kedua format itu yang saat ini paling banyak digunakan untuk menulis inventory.

Berikut contoh inventory file yang sangat simpel:
```
$ cat inventory
web1.example.com
web2.example.com
db1.example.com
192.168.56.115
```
Alamat-alamat host tersebut dapat dikelompokkan menjadi grup seperti contoh dibawah ini.
```
$ cat inventory
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com
192.168.56.115

[prod:children]
webservers
dbservers
```
Pada contoh di atas, web1 dan web2 dapat dipanggil sekaligus menggunakan nama grup "webservers". Begitu pula dengan "dbservers" yang sudah mencakup db1 dan 192.168.56.115. Sedangkan nama grup "prod" dapat digunakan untuk memanggil alamat yang ada pada grup webservers dan dbservers sekaligus.

Ada 2 grup yang selalu ada pada inventory, yaitu:
- grup "all" yang mencakup semua alamat yang ada di dalam inventory file.
- grup "ungrouped" mencakup semua alamat yang tidak masuk di grup manapun di dalam inventory file.

## Menggunakan range
Selain menuliskan alamat satu per satu, kita juga bisa menuliskannya sekaligus menggunakan **range**. Range bisa berupa nomor atau alfabet dan dituliskan menggunakan format berikut:
`[START:END]`

Berikut contoh penggunaan range:
```
$ cat inventory
[webservers]
web[1:2].example.com

[dbservers]
192.168.56.[11:13]

[dnsservers]
[a:c].dns.example.com
```
Dari contoh diatas terlihat beberapa penggunaan range berikut:
- `web[1:2].example.com` menunjukkan alamat web1.example.com dan web2.example.com.
- `192.168.56.[11:13]` menunjukkan alamat 192.168.56.11, 192.168.56.12, dan 192.168.56.13.
- `[a:c].dns.example.com` menunjukkan alamat a.dns.example.com, b.dns.example.com, dan c.dns.example.com.

## Verifikasi
Selain dengan membuka file inventory, kita juga bisa mengecek apakah suatu alamat ada di dalam inventory file menggunakan command berikut:
```
$ ansible -i inventory web1.example.com --list-hosts
  hosts (1):
    web1.example.com
```
Kita juga bisa mengecek isi dari suatu grup di dalam inventory file:
```
$ ansible -i inventory dnsservers --list-hosts
  hosts (3):
    a.dns.example.com
    b.dns.example.com
    c.dns.example.com
```