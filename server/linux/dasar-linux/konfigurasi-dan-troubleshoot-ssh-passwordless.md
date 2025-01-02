---
title: Konfigurasi dan troubleshoot ssh passwordless
description: 
published: true
date: 2025-01-02T10:52:44.343Z
tags: server, linux
editor: markdown
dateCreated: 2025-01-02T10:52:44.343Z
---

Biasanya ketika kita melakukan login ke dalam server, kita menggunakan kombinasi username dan password untuk authentikasi ke dalam server. Ada metode authentikasi lain yang dapat kita gunakan yaitu menggunakan *ssh keypair*. Metode ini menggunakan kombinasi SSH public dan private key sehingga kita tidak perlu menginput username dan password seperti sebelumnya, sehingga sering disebut juga sebagai *SSH Passwordless*.

SSH passwordless biasanya digunakan ketika kita ingin memanage server menggunakan automation tools seperti ansible, sehingga ansible tidak memunculkan prompt login setiap kali ingin connect ke suatu server. Atau bisa juga digunakan untuk meningkatkan keamanan port ssh sehingga kita bisa mematikan metode "PasswordAuthentication" di dalam konfigurasi sshd untuk mencegah brute force username+password.

## Konfigurasi SSH Passwordless
Disini kita akan mengkonfigurasi ssh passwordless sehingga svr01 (192.168.64.14) akan konek ke svr02 (192.168.64.15) tanpa menggunakan username+password. SSH keypair akan di create di svr01.

```
[centos@svr01 ~]$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/centos/.ssh/id_rsa):
Created directory '/home/centos/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/centos/.ssh/id_rsa
Your public key has been saved in /home/centos/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:CNdrRzlQNxiUVmjm68OpvZLGJmLa/tM7+ci+mVSTrSQ centos@svr01
The key's randomart image is:
+---[RSA 3072]----+
|        .o+*+    |
|       . .Bo .   |
|    . . .=+      |
|     o . o.+     |
|      . E *..    |
|       . =.o     |
|       o.=..     |
|   .o ooX==      |
|  .+oo.*XB+o     |
+----[SHA256]-----+
```

Pada contoh di atas, kita membuat ssh keypair rsa tanpa passphrase dengan lokasi default di folder `/home/namauser/.ssh`:
- File `id_rsa` merupakan private key untuk login ke server tujuan. Simpan key ini baik-baik dan jangan dibagian ke publik.
- File `id_rsa.pub` merupakan public key yang akan di copykan ke server tujuan yang akan di ssh.

Ada 2 cara mengkopi public key ke server tujuan:
#### Cara 1
Kopi manual isi file `id_rsa.pub` di svr01 ke lokasi `.ssh/authorized_keys` di server 2
```
[centos@svr01 ~]$ cat .ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDAwJ+uRfIak1PGAS2WCaHyDtXirAni+QKUGZ6kVUbbyHbe/KxM86gFepuxep/Fj9BbY0aWkJmFKLeiwsSRp7DrkzwV0iZU63PEYnxTZeUXblx4rUeOhhmTCekCPOdBIayzuThIS8lB7Dta4oFW+g2Mud3RZRSIM1Q3zZShCUWcs+YNnmJJgxzWm2vf7ojSc8qiWSLIt5WkVMurAJtSd+ieyItDOqSjTkKsgIscY0zLl9XCQsvS7zQwXR16SEBxDNTZ1u0zIQqPpz+kq4oxMfmUoCX2KOEoYMqjryTNAk+GyTzS4whAcnzpg+PofuQCyJqlZTXeN59+I/FG1IBBOlFLEbP5IQrMXUDDmwicP2RMmqvpNEEQAFD81zH5lY6pgdU5UX5FUsBhhzfYKGc56BaxNmMvdWlbZ0+fZv6OFsmfM6P4v+pdAi6Ae8+wOMFHoDCjHSzNttlBd4Gc5SAoA8yrHEPM7mLx9cY/OdEuuO89n9cQWpWCmZuEAW3TiS8Aw98= centos@svr01
```
```
[centos@svr02 ~]$ pwd
/home/centos
[centos@svr02 ~]$ mkdir .ssh
[centos@svr02 ~]$ vi .ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDAwJ+uRfIak1PGAS2WCaHyDtXirAni+QKUGZ6kVUbbyHbe/KxM86gFepuxep/Fj9BbY0aWkJmFKLeiwsSRp7DrkzwV0iZU63PEYnxTZeUXblx4rUeOhhmTCekCPOdBIayzuThIS8lB7Dta4oFW+g2Mud3RZRSIM1Q3zZShCUWcs+YNnmJJgxzWm2vf7ojSc8qiWSLIt5WkVMurAJtSd+ieyItDOqSjTkKsgIscY0zLl9XCQsvS7zQwXR16SEBxDNTZ1u0zIQqPpz+kq4oxMfmUoCX2KOEoYMqjryTNAk+GyTzS4whAcnzpg+PofuQCyJqlZTXeN59+I/FG1IBBOlFLEbP5IQrMXUDDmwicP2RMmqvpNEEQAFD81zH5lY6pgdU5UX5FUsBhhzfYKGc56BaxNmMvdWlbZ0+fZv6OFsmfM6P4v+pdAi6Ae8+wOMFHoDCjHSzNttlBd4Gc5SAoA8yrHEPM7mLx9cY/OdEuuO89n9cQWpWCmZuEAW3TiS8Aw98= centos@svr01
```

#### Cara 2
Menggunakan command `ssh-copy-id`, syaratnya koneksi ssh dari svr01 ke svr02 sudah dibuka terlebih dahulu dan sudah bisa login menggunakan username+password sebelumnya.
```
[centos@svr01 ~]$ ssh-copy-id -i .ssh/id_rsa.pub centos@192.168.64.15
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ".ssh/id_rsa.pub"
The authenticity of host '192.168.64.15 (192.168.64.15)' can't be established.
ED25519 key fingerprint is SHA256:tu4uNooS9JbtW4UDSlloAcfkK497xwFkC5EwyfJM3aQ.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
centos@192.168.64.15's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'centos@192.168.64.15'"
and check to make sure that only the key(s) you wanted were added.
```

Jika sudah, maka tinggal kita test ssh seperti biasa dari svr01 ke svr02.
```
[centos@svr01 ~]$ ssh centos@192.168.64.15
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Sun Aug 27 03:03:23 2023 from 192.168.64.14
[centos@svr02 ~]$
```

## Troubleshoot SSH Passwordless
Berikut beberapa contoh issue ketika setup ssh passwordless.
- Tetap ada prompt password ketika ssh
    ```
    [centos@svr01 ~]$ ssh centos@192.168.64.15
    centos@192.168.64.15's password:
    ```

    Beberapa kemungkinan solusi:
    Pastikan `PubkeyAuthentication` sudah di enable di file `/etc/ssh/sshd_config`
    ```
    [centos@svr02 ~]$ sudo grep PubkeyAuthentication /etc/ssh/sshd_config
    PubkeyAuthentication yes
    ```

    Jika belum, aktifkan `PubkeyAuthentication` dan restart sshd
    ```
    [centos@svr02 ~]$ sudo systemctl restart sshd
    ```

    Selanjutnya pastikan folder `.ssh` hanya writable oleh user ownernya. Biasanya menggunakan permission 700.
    ```
    [centos@svr02 ~]$ chmod 700 .ssh
    [centos@svr02 ~]$ ls -ld .ssh
    drwx------ 2 centos centos 29 Aug 27 03:01 .ssh
    ```

    Lalu pastikan juga file `.ssh/authorized_keys` mempunyai permission read and write oleh user ownernya. Biasanya menggunakan permission 600.
    ```
    [centos@svr02 ~]$ chmod 600 .ssh/authorized_keys
    [centos@svr02 ~]$ ls -l .ssh
    total 4
    -rw------- 1 centos centos 566 Aug 27 03:01 authorized_keys
    ```

    Terakhir, jika SELinux dalam keadaan *Enforcing* di OS, maka pastikan juga label selinux contextnya sudah sesuai untuk folder `.ssh` dan file `authorized_keys`. Untuk merestore context yang benar, kita bisa menggunakan command `restorecon`.
    ```
    [centos@svr02 ~]$ getenforce
    Enforcing
    [centos@svr02 ~]$ restorecon -R -v ~/.ssh
    Relabeled /home/centos/.ssh from unconfined_u:object_r:user_home_dir_t:s0 to unconfined_u:object_r:ssh_home_t:s0
    Relabeled /home/centos/.ssh/authorized_keys from unconfined_u:object_r:user_home_t:s0 to unconfined_u:object_r:ssh_home_t:s0
    [centos@svr02 ~]$ ls -laZ .ssh
    total 4
    drwxr-xr-x. 2 centos centos unconfined_u:object_r:ssh_home_t:s0   29 Aug 27 05:52 .
    drwx------. 3 centos centos system_u:object_r:user_home_dir_t:s0  95 Aug 27 05:52 ..
    -rw-------. 1 centos centos unconfined_u:object_r:ssh_home_t:s0  566 Aug 27 05:52 authorized_keys
    ```

- Muncul "WARNING: UNPROTECTED PRIVATE KEY FILE!" atau error bad permissions.
    ```
    [centos@svr01 ~]$ ssh centos@192.168.64.15
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    @         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
    @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    Permissions 0644 for '/home/centos/.ssh/id_rsa' are too open.
    It is required that your private key files are NOT accessible by others.
    This private key will be ignored.
    Load key "/home/centos/.ssh/id_rsa": bad permissions
    centos@192.168.64.15's password:
    ```

    Pastikan private key `id_rsa` hanya bisa di read oleh user ownernya saja. Biasanya menggunakan permission 400 atau 600.
    ```
    [centos@svr01 ~]$ chmod 400 .ssh/id_rsa
    [centos@svr01 ~]$ ls -l .ssh/id_rsa
    -r-------- 1 centos centos 2602 Aug 27 02:49 .ssh/id_rsa
    ``` 
