---
title: Pengenalan Ansible
description: 
published: true
date: 2024-12-28T12:04:11.365Z
tags: automation, ansible
editor: markdown
dateCreated: 2024-12-28T12:04:11.365Z
---

Pada umumnya kita memanage server dengan cara manual, seperti memasukkan command melalui CLI atau melalui GUI. Server-server yang kita manage tersebut dikonfigurasi secara independen dan terpisah. Cara seperti ini memiliki beberapa kekurangan, misalnya:
- Ada langkah-langkah yang terlupa atau salah memasukkan command.
- Sulit melakukan verifikasi apakah command yang dimasukkan sudah sesuai atau belum.
- Jika mengkonfigurasi banyak server yang identik, seringkali terjadi perbedaan hasil yang diinginkan.
- Sulit melakukan maintenance atau troubleshoot jika server berjumlah banyak.

Hal-hal tersebut dapat kita hindari dengan melakukan *automation* (otomasi). Dengan automation, kita bisa memanage ataupun mengkonfigurasi server dalam jumlah banyak secara cepat dan dengan konfigurasi yang sama persis. Dengan automation juga kita dapat menghemat waktu dalam melakukan task-task yang bersifat operasional, sehingga kita dapat memiliki waktu luang untuk mengerjakan hal yang lain.

## Otomasi menggunakan Ansible
Ansible merupakan salah satu platform otomasi yang banyak digunakan. Ansible menggunakan bahasa otomasi yang simple yang disimpan dalam *ansible playbook*. Selain itu ansible bersifat *agentless* yang berarti kita tidak perlu menginstall agent atau software tambahan di client, tetapi cukup terkoneksi menggunakan SSH atau WinRM.

Ada 2 tipe node jika kita menggunakan ansible:
- control node, tempat kita menjalankan command ansible atau ansible playbook
- managed node, client yang akan di manage oleh ansible control node

Alamat IP atau host dari managed node dapat disimpan di control node dalam bentuk *inventory* file. Node di dalam inventory dapat dibuat grup untuk memudahkan ketika ingin dipilih. Ansible akan mengeksekusi *play* atau kumpulan dari satu atau beberapa *task* yang disimpan di *playbook* dengan format YAML.

Setiap task di dalam playbook akan menjalankan *ansible module* yang mempunyai fungsi berbeda. Ansible dilengkapi dengan sejumlah module bawaan yang dapat langsung kita gunakan untuk melakukan otomasi. Module pada ansible akan mengecek *state* atau kondisi aktual apakah sama dengan yang diinginkan di playbook. Jika tidak sama, maka ansible akan menjalankan task-task di dalam playbook untuk mencapai *state* tersebut. Apabila ada task yang *failed* atau gagal, *by default* ansible akan berhenti dan tidak mengeksekusi task-task selanjutnya.

Playbook di ansible umumnya bersifat idempotent, yaitu ansible hanya akan mengeksekusi task apabila *state* aktual tidak sama dengan *state* yang kita inginkan di playbook. Sehingga jika kita mengeksekusi playbook berulang kali maka tidak ada perubahan yang akan dilakukan jika *state*-nya sudah sama. Tetapi hal ini tidak berlaku jika kita menjalankan *arbitrary command* di ansible misalnya ketika menggunakan module shell.

## Beberapa keunggulan ansible
- Meminimalisir *human error*
Karena ansible merupakan code yang dieksekusi berulang kali, maka seharusnya konfigurasi di server atau perangkat akan selalu sama karena tidak ada code yang berubah. Selain itu, code ansible dapat di-review dan di-*crosscheck* oleh beberapa pihak sebelum dieksekusi.
- Infrastructure as Code
Saat ini metode IaC sedang banyak dilirik karena setiap *state* dan konfigurasi di dalam infrastruktur dapat direpresentasikan berupa code yang dapat disimpan di *version control system* dan dimanage serupa dengan developer software memanage source code aplikasi. Code IaC tersebut juga dapat dengan mudah dieksekusi apabila kita ingin melakukan restore infrastructure atau rebuild pada environment yang berbeda.
- *Cross-platform*
Ansible dapat digunakan pada sistem operasi Linux, Windows, UNIX, dll. Dapat juga digunakan untuk memanage server, perangkat jaringan baik itu di environment fisik, virtual, ataupun di cloud.