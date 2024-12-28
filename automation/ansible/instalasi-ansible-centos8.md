---
title: Instalasi Ansible di Centos 8
description: 
published: true
date: 2024-12-28T12:06:26.398Z
tags: automation, ansible
editor: markdown
dateCreated: 2024-12-28T12:06:26.398Z
---

Ansible hanya diinstal pada komputer/server yang berfungsi sebagai contol node, yaitu pada node dimana playbook atau command ansible akan dijalankan. Perangkat lain yang di manage oleh ansible (managed node) tidak perlu diinstal ansible juga, tetapi cukup ada konektivitas ssh atau WinRM dari control node. Saat ini control node yang digunakan hanya bisa menggunakan OS Linux atau Unix. Sedangkan untuk managed node dapat menggunakan OS apa saja termasuk Windows.

Ansible sangat mudah untuk diinstal. Disini kita akan mencoba menginstall ansible pada OS Centos 8. Di Centos 8 package ansible tersedia pada repository epel. Sehingga kita perlu menginstall terlebih dahulu repository epel tersebut. Ansible membutuhkan package python3 sebagai dependency-nya. Biasanya ketika kita menginstall ansible pada Centos 8, package python3 yang diperlukan juga akan ikut terinstall.

- Instal epel repository

  `$ sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

- Enable powertool repository (karena ada beberapa dependency epel yang berada disitu)

  `$ sudo dnf config-manager --set-enabled powertools`

- Selanjutnya kita install ansible

  `$ sudo dnf install ansible`

- Cek apakah ansible sudah terinstall

  ```
  $ ansible --version
  ansible 2.9.21
    config file = /etc/ansible/ansible.cfg
    configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
    ansible python module location = /usr/lib/python3.6/site-packages/ansible
    executable location = /usr/bin/ansible
    python version = 3.6.8 (default, Aug 24 2020, 17:57:11) [GCC 8.3.1 20191121 (Red Hat 8.3.1-5)]
  ```

## Managed Nodes
Ada beberapa macam managed nodes atau perangkat-perangkat yang dapat di manage oleh ansible:

- Linux / UNIX
 Umumnya managed nodes ini cukup terinstall package python yang diperlukan ansible module untuk bekerja. Misalnya jika kita butuh menginstall python3 di Centos 8 cukup menginstallnya dengan command berikut:

  `$ sudo dnf install python36`

 - Windows based
 Ada beberapa ansible module yang memang digunakan untuk memanage Windows system. Biasanya dibutuhkan requirement berupa powershell dan .NET framework yang terinstall di managed nodes.

 - Network Devices
 Perangkat-perangkat jaringan seperti router dan switch juga dapat dimanage oleh ansible. Ansible mempunyai module-module untuk perangkat seperti Cisco IOS/IOS XR/NX-OS, Juniper Junos, dan yang lainnya. Jika network devices tersebut tidak bisa menjalankan python, maka module ansible akan dieksekusi pada control node yang mempunyai package python. Selanjutnya control node akan berkomunikasi ke network devices menggunakan CLI over SSH, XML over SSH, atau API over HTTP(S).