---
title: File Konfigurasi Ansible
description: 
published: true
date: 2025-01-01T13:56:38.685Z
tags: automation, ansible
editor: markdown
dateCreated: 2025-01-01T13:56:38.685Z
---

Kita dapat merubah konfigurasi atau behaviour dari ansible menggunakan file konfigurasi ansible. Jika ada lebih dari satu file konfigurasi, ansible akan menggunakan file konfigurasi dengan prioritas sebagai berikut:
- File yang dituju oleh environment variable `ANSIBLE_CONFIG`
- File konfigurasi yang berada di direktori yang sama dengan command ansible yang dijalankan, `./ansible.cfg`
- File konfigurasi yang berlokasi di homedir, yaitu `~/ansible.cfg`
- File konfigurasi yang terletak di `/etc/ansible/ansible.cfg`

Perlu diingat jika ada lebih dari satu file ansible.cfg, ansible tidak akan menggabungkan konfigurasi yang ada di beberapa file tersebut. Tetapi ansible hanya menggunakan salah satu dari file ansible.cfg sesuai prioritas di atas. Ansible akan menggunakan konfigurasi default apabila suatu konfigurasi tidak kita atur secara eksplisit di dalam file ansible.cfg.

Best practices yang umum digunakan adalah dengan membuat file ansible.cfg di dalam direktori tempat kita menjalankan command ansible. Biasanya di dalam direktori tersebut juga ada file lain seperti inventory dan playbook. Cara ini akan memudahkan kita apabila ada beberapa environment atau project dan masing-masing project tersebut memiliki konfigurasi berbeda. Setiap project akan memiliki ansible.cfg di direktorinya sendiri sehingga konfigurasinya tidak tercampur satu sama lain.

Kita bisa mengecek file konfigurasi yang sedang aktif dengan menggunakan parameter `--version` atau menjalankan ansible dengan opsi `-v`.
```
$ ansible --version
ansible 2.9.21
  config file = /etc/ansible/ansible.cfg
  ...output dipotong...
```
```
$ ansible -i inventory2 all -m ping -v
Using /etc/ansible/ansible.cfg as config file
...output dipotong...
```

## Pengaturan Umum
Di dalam ansible.cfg terdapat beberapa sections yang namanya ditandai dengan kurung siku `[ ]` seperti contoh berikut:
```
$ cat ansible.cfg
[defaults]
; menunjukkan lokasi default inventory file
inventory = ./inventory
; user yang dipakai ansible untuk login ke server tujuan
remote_user = user
; prompt untuk ssh password, false jika menggunakan ssh passwordless
ask_pass = false

[privilege_escalation]
# melakukan switch (pindah) user di server tujuan
become = true
# metode switch user, biasanya sudo atau su
become_method = sudo
# user tujuan ketika switching menggunakan become
become_user = root
# prompt password ketika switching, false jika tidak
become_ask_pass = false
```

Contoh di atas menunjukkan beberapa pengaturan dasar dari ansible, khususnya untuk mengatur komunikasi dengan managed host. Beberapa hal yang diatur oleh pengaturan di atas:
- Lokasi default inventory file yang digunakan
- Protokol yang digunakan untuk berkomunikasi, by default menggunakan SSH
- User yang digunakan untuk meremote managed host
- Privilege Escalation di server tujuan
- Pemilihan prompt password atau tidak untuk ssh password dan sudo

### Lokasi inventory
Kita bisa merubah lokasi default dari inventory file dengan menggunakan direktif `inventory` pada sections `[defaults]`
```
[defaults]
inventory = ./inventory
```

### Pengaturan Koneksi
Kita bisa mengatur bagaimana ansible terkoneksi ke managed host menggunakan beberapa direktif di sections `[default]`. By default ansible menggunakan protokol ssh dan akan menggunakan user yang sedang aktif. Kita bisa merubah user yang digunakan dengan direktif `remote_user = name_user` dan mengatur apakah akan menampilkan prompt password dengan direktif `ask_pass = true/false`.

### Eskalasi Privilege
Jika command atau module yang kita jalankan di managed host membutuhkan akses administratif sebagai root, kita bisa mengatur eskalasi privilege pada sections `[privilege_escalation]` di ansible.cfg.

Beberapa direktif yang umum digunakan yaitu `become = true` untuk enable privilege escalation, `become_method = sudo` untuk melakukan eskalasi dengan sudo, `become_user = root` untuk menentukan user tujuan eskalasi, dan `become_ask_pass` untuk enable/disable prompt password ketika eskalasi user.

### Koneksi lokal
By default ansible menggunakan jenis protokol **smart** SSH connection ketika terkoneksi ke managed host. Pengecualiannya ketika kita ingin terkoneksi ke localhost, maka ansible akan menggunakan jenis protokol **local** yang akan mengeksekusi command langsung di localhost tanpa melalui ssh. Ini berarti ansible akan menggunakan current user ketika mengeksekusi command dan bukan menggunakan remote_user seperti ketika terkoneksi ke managed host yang lain.

Kita bisa menggunakan host variable untuk mengubah pengaturan ini. Misalnya kita ingin menggunakan protokol smart ssh ketika ingin terkoneksi ke localhost, maka cukup buat subdirektori `./host_vars` dan buat file dengan nama `localhost` yang berisi direktif `ansible_connection: smart` di dalamnya.
```
$ mkdir host_vars
$ echo 'ansible_connection: smart' > host_vars/localhost
$ cat host_vars/localhost
ansible_connection: smart
```

Begitu pula sebaliknya jika kita ingin menggunakan protokol local untuk terkoneksi ke 127.0.0.1, maka cukup buat subdirektori `./host_vars` dan buat file dengan nama `127.0.0.1` yang berisi direktif `ansible_connection: local` di dalamnya.
```
$ echo 'ansible_connection: local' > host_vars/127.0.0.1
$ cat host_vars/127.0.0.1
ansible_connection: local
```

Sebagai catatan, jika kita ingin merubah pengaturan untuk satu grup sekaligus, maka buat *group variables* dengan memasukkan direktif di atas pada direktori `./group_vars`.

### Comments
Comments di dalam file konfigurasi ansible dapat dimulai dengan tanda pagar `#` atau titik-koma `;` seperti pada contoh di atas.