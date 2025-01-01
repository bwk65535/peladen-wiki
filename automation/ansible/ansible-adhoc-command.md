---
title: Menjalankan Ansible Ad hoc Command
description: 
published: true
date: 2025-01-01T13:59:54.798Z
tags: automation, ansible
editor: markdown
dateCreated: 2025-01-01T13:59:54.798Z
---

Ansible adhoc command merupakan cara menjalankan command ansible secara cepat tanpa perlu membuat playbook. Misalnya kita perlu mengecek dengan cepat status dari beberapa server atau melakukan restart service mendadak untuk beberapa server maka kita bisa menjalankan adhoc command. Adhoc command memiliki beberapa keterbatasan dibandingkan dengan menggunakan playbook.

## Menjalankan adhoc command
Ansible adhoc command memiliki struktur seperti berikut:
```
ansible host_pattern -m module [-a 'module arguments'] [-i invenory]
```
- `host_pattern` menunjuk pada nama host atau group pada inventory
- `-m` menunjuk pada nama module yang ingin digunakan
- `-a` menunjukkan argument yang ingin digunakan dengan module tersebut

Berikut beberapa contoh penggunaan adhoc command:
- Melakukan "ping" ke semua managed host. Ping di sini bukanlah ICMP ping seperti di network, akan tetapi mengecek apakah managed host bisa diakses secara passwordless dan bisa menjalankan module berbasis python.
  ```
  $ ansible -i inventory2 all -m ping
  web2.example.com | SUCCESS => {
      "ansible_facts": {
          "discovered_interpreter_python": "/usr/libexec/platform-python"
      },
      "changed": false,
      "ping": "pong"
  }
  web1.example.com | SUCCESS => {
      "ansible_facts": {
          "discovered_interpreter_python": "/usr/libexec/platform-python"
      },
      "changed": false,
      "ping": "pong"
  }
  db1.example.com | SUCCESS => {
      "ansible_facts": {
          "discovered_interpreter_python": "/usr/libexec/platform-python"
      },
      "changed": false,
      "ping": "pong"
  }
  db2.example.com | SUCCESS => {
      "ansible_facts": {
          "discovered_interpreter_python": "/usr/libexec/platform-python"
      },
      "changed": false,
      "ping": "pong"
  }
  ```

- Mengecek uptime dari managed hosts
  ```
  $ ansible -i inventory2 all -m command -a 'uptime'
  db2.example.com | CHANGED | rc=0 >>
  07:53:02 up 6 min,  1 user,  load average: 0.16, 0.13, 0.08
  db1.example.com | CHANGED | rc=0 >>
  07:53:02 up 7 min,  1 user,  load average: 0.00, 0.09, 0.07
  web2.example.com | CHANGED | rc=0 >>
  07:53:02 up 7 min,  1 user,  load average: 0.06, 0.09, 0.07
  web1.example.com | CHANGED | rc=0 >>
  07:53:02 up 8 min,  1 user,  load average: 0.00, 0.06, 0.06
  ```
- Mengecek ip address utama yang dimiliki managed hosts
  ```
  $ ansible -i inventory2 all -m command -a 'hostname -I'
  web1.example.com | CHANGED | rc=0 >>
  10.0.2.15 192.168.56.112
  web2.example.com | CHANGED | rc=0 >>
  10.0.2.15 192.168.56.113
  db2.example.com | CHANGED | rc=0 >>
  10.0.2.15 192.168.56.115
  db1.example.com | CHANGED | rc=0 >>
  10.0.2.15 192.168.56.114
  ```

## Menjalankan ansible module dengan adhoc command
Module merupakan tools yang digunakan ansible untuk mengerjakan task-task tertentu. Ansible menyediakan built-in modules yang memiliki beberapa fungsi berbeda. Selain itu kita juga bisa membuat module sendiri untuk digunakan dengan ansible. Kita bisa mengecek dokumentasi module melalui terminal dengan menggunakan command `ansible-doc`, misalnya:
- `$ ansible-doc -l`: list semua module yang terinstall
- `$ ansible-doc nama_modules`: melihat dokumentasi dari nama module yang kita inginkan

Banyak sekali module bawaan dari ansible yang sangat berguna. Berikut beberapa module yang biasa digunakan:
- Files modules
  - copy: mengkopi file dari localhost ke managed host
  - file: mengeset properti dari file, seperti permission, dll.
  - lineinfile: memastikan suatu line/baris harus ada di dalam file
  - synchronize: sinkronisasi konten menggunakan rsync
- Sofware package modules:
  - package: untuk memanage package sesuai dengan packaga manager yang digunakan OS
  - yum: manage package menggunakan yum
  - apt: manage package menggunakan apt
  - dnf: manage package menggunakan dnf
  - gem: untuk memanage ruby gems
  - pip: untuk memanage python packages
- System modules:
  - firewalld: memanage firewall menggunakan firewalld
  - reboot: mereboot mesin
  - service: untuk memanage service
  - user: untuk memanage akun user
- Net Tools modules:
  - get_url: download file menggunakan http(s) atau ftp
  - nmcli: memanage network
  - uri: berinteraksi dengan web services

Beberapa module membutuhkan argument untuk menjalankannya, serperti module command atau shell. Argument dimasukan setelah opsi `-a` seperti pada penjelasan di atas. Jika ada lebih dari satu argument, maka bisa dipisahkan dengan spasi. Sebagian besar module bawaan ansible sudah bersifat idempotent, yaitu jika command tersebut mendeteksi state atau hasil command sudah seperti yang diinginkan maka command tersebut tidak akan dijalankan kembali. Jika sudah bersifat idempotent, maka walaupun command atau module tersebut dijalankan berulang kali akan aman dan tidak dieksekusi berulang kali.

## Module shell, command, dan raw
Kita bisa menjalankan command terminal linux menggunakan ansible dengan module **command**, **shell** atau **raw**. Perbedaan ketiganya adalah untuk module command tidak diproses oleh shell pada manages host, sehingga kita tidak bisa menggunakan shell environment variables dan melakukan shell operations serperti redirection dan pipe. Jika kita butuh shell processing seperti contoh di atas, maka module shell dapat digunakan sebagai ganti dari module command.

Module command dan shell membutuhkan python terinstall pada managed host supaya dapat dijalankan. Apabila karena suatu hal python tidak dapat diinstal pada managed host, maka kita bisa menggunakan module raw yang akan mengirim perintah langsung ke remote shell. Contoh dari penggunaan module raw misalnya untuk mengirim perintah ke network router yang tidak bisa diinstal python. Contoh lain apabila python belum terinstal pada managed host, maka kita bisa menginstal python terlebih dahulu menggunakan module raw.

Sedikit catatan, untuk ketiga module ini sulit dijalankan secara idempotent seperti module lainnya. Lebih baik menggunakan module yang spesifik untuk tugas tertentu, kecuali apabila kita ingin menjalankan adhoc command secara cepat. Atau jika memang tidak ada module lain yang bisa melakukan hal yang kita inginkan selain dari command manual. Jika memang dibutuhkan, gunakan module "command" terlebih dahulu baru gunakan module "shell" atau "raw" jika kita memang butuh menggunakan fitur yang dimilikinya.