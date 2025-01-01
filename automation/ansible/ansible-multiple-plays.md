---
title: Ansible Multiple Plays
description: 
published: true
date: 2025-01-01T14:43:23.657Z
tags: automation, ansible
editor: markdown
dateCreated: 2025-01-01T14:43:23.657Z
---

Play di dalam ansible playbook merupakan sebuah list dengan format YAML. Berarti kita bisa memasukkan lebih dari satu play (multiple plays) di dalam sebuah playbook. Masing-masing play dapat kita gunakan untuk membuat task-task yang dapat di-apply ke managed host atau kumpulan dari managed host yang berbeda. Ini dapat memudahkan kita membuat playbook untuk mengorkestrasi deployment yang lebih kompleks.

Contoh membuat multiple plays di dalam playbook. Playbook ini akan menginstall dan menjalankan apache httpd server dan mariadb-server pada OS Centos 8:
```
$ cat site.yml
---
- name: Install and start Apache HTTPD
  hosts: webservers
  tasks:
    - name: httpd package is present
      dnf:
        name: httpd
        state: present

    - name: correct index.html is present
      copy:
        src: files/index.html
        dest: /var/www/html/index.html

    - name: httpd is restarted
      service:
        name: httpd
        state: started
        enabled: true

- name: Install and start MariaDB
  hosts: dbservers
  tasks:
    - name: mariadb package is present
      dnf:
        name: mariadb-server
        state: present

    - name: mariadb is restarted
      service:
        name: mariadb
        state: started
        enabled: true
```

Pada contoh playbook di atas terdapat dua play, yaitu:
- `Install and start Apache HTTPD` yang dijalankan untuk group `webservers`
- `Install and start MariaDB` yang dijalankan untuk group `dbservers`

Masing-masing play mempunyai lebih dari satu task, yang intinya akan menginstal dan menjalankan package yang diinginkan untuk masing-masing group dari managed hosts.

Selanjutnya kita coba jalankan playbook tersebut:
```
$ ansible-playbook site.yml -b

PLAY [Install and start Apache HTTPD] ******************************************************

TASK [Gathering Facts] *********************************************************************
ok: [web2.example.com]
ok: [web1.example.com]

TASK [httpd package is present] ************************************************************
changed: [web2.example.com]
changed: [web1.example.com]

TASK [correct index.html is present] *******************************************************
ok: [web2.example.com]
ok: [web1.example.com]

TASK [httpd is restarted] ******************************************************************
changed: [web2.example.com]
changed: [web1.example.com]

PLAY [Install and start MariaDB] ***********************************************************

TASK [Gathering Facts] *********************************************************************
ok: [db1.example.com]
ok: [db2.example.com]

TASK [mariadb package is present] **********************************************************
changed: [db1.example.com]
changed: [db2.example.com]

TASK [mariadb is restarted] ****************************************************************
changed: [db2.example.com]
changed: [db1.example.com]

PLAY RECAP *********************************************************************************
db1.example.com            : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
db2.example.com            : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web1.example.com           : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2.example.com           : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Terlihat di atas play dan task dieksekusi secara berurutan, play instalasi httpd akan dijalankan terlebih dahulu baru play instalasi mariadb kemudian.

Untuk memudahkan, kita bisa mengecek secara cepat package yang terinstall menggunakan ansible adhoc command:
```
$ ansible webservers -m command -a 'dnf list --installed httpd'
web1.example.com | CHANGED | rc=0 >>
Installed Packages
httpd.x86_64          2.4.37-39.module_el8.4.0+778+c970deab           @appstream
web2.example.com | CHANGED | rc=0 >>
Installed Packages
httpd.x86_64          2.4.37-39.module_el8.4.0+778+c970deab           @appstream

$ ansible dbservers -m command -a 'dnf list --installed mariadb-server'
db1.example.com | CHANGED | rc=0 >>
Installed Packages
mariadb-server.x86_64     3:10.3.28-1.module_el8.3.0+757+d382997d     @appstream
db2.example.com | CHANGED | rc=0 >>
Installed Packages
mariadb-server.x86_64     3:10.3.28-1.module_el8.3.0+757+d382997d     @appstream
```
```
$ ansible webservers -m command -a 'systemctl is-active httpd'
web2.example.com | CHANGED | rc=0 >>
active
web1.example.com | CHANGED | rc=0 >>
active

$ ansible dbservers -m command -a 'systemctl is-active mariadb'
db2.example.com | CHANGED | rc=0 >>
active
db1.example.com | CHANGED | rc=0 >>
active
```