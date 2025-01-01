---
title: Menggunakan Environment Variables
description: 
published: true
date: 2025-01-01T14:51:17.740Z
tags: server, linux
editor: markdown
dateCreated: 2025-01-01T14:51:17.740Z
---

Berbeda dengan *shell variables* yang hanya dapat digunakan oleh shell, *environment variables* dapat digunakan oleh shell dan program yang dijalankan dari shell tersebut.

Kita bisa mengubah shell variables menjadi environment variables menggunakan command *export*:
```
$ DB_NAME=testdb
$ export DB_NAME
$ env | grep DB_NAME
DB_NAME=testdb
```
Atau bisa juga langsung dalam satu line:
```
$ export DB_NAME=testdb
```

Untuk me-list seluruh environment variables, bisa menggunakan command *env*:
```
$ env
...output dipotong...
DB_NAME=testdb
MAIL=/var/spool/mail/redhat
PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/redhat/.local/bin:/home/redhat/bin
PWD=/home/redhat
LANG=en_US.UTF-8
SELINUX_LEVEL_REQUESTED=
HISTCONTROL=ignoredups
SHLVL=1
HOME=/home/redhat
...output dipotong...
```

## Setting variables secara otomatis
Kita bisa mengeset variables, baik shell atau environment variables secara otomatis ketika shell dijalankan dengan menggunakan Bash startup scripts. Ketika bash dijalankan, startup scripts tersebut akan dieksekusi untuk menginisialisasi shell environment.

Ada beberapa bash startup scripts yang dapat digunakan untuk mengkonfigurasi variables. Beberapa startup scripts itu akan dieksekusi atau tidak bergantung pada bagaimana shell di jalankan. Shell dapat dijalankan berupa *interactive login shell*, *interactive non-login shell*, atau *shell script*.

Sedangkan startup scripts yang dapat kita gunakan ada di:
- /etc/profile
- /etc/bashrc
- ~/.bash_profile
- ~/.bashrc

Sebagai contoh untuk mengeset default editor yang digunakan user tertentu, kita bisa mengeset variables EDITOR pada file ~/.bashrc:
```
$ vi ~/.bashrc

# .bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions
export EDITOR=vi
```

## Menghapus variables
Untuk menghapus atau unset dan unexport variable, kita bisa menggunakan command `unset`:
```
$ env | grep DB_NAME
DB_NAME=testdb
$ echo $DB_NAME
testdb
$ unset DB_NAME
$ env | grep DB_NAME

$ echo $DB_NAME

```

Sedangkan untuk unexport variable tanpa unset, bisa menggunakan command `export -n`:
```
[redhat@rhel7-vm ~]$ export -n DB_NAME=testdb
[redhat@rhel7-vm ~]$ env | grep DB_NAME

[redhat@rhel7-vm ~]$ echo $DB_NAME
testdb
```

Catatan:
- Secara konvensi, shell dan environment variables yang diset otomatis oleh shell mempunyai nama UPPERCASE.
- Untuk mengubah setting yang ingin diaplikasikan untuk semua *user accounts*, cara yang umum digunakan adalah dengan membuat script dengan nama file berakhiran *.sh* di direktori */etc/profile.d*.