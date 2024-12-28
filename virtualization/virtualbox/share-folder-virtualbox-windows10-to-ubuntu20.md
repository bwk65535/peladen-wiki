---
title: Share folder Virtualbox dari Windows 10 host ke Ubuntu 20.04 Server Guest
description: 
published: true
date: 2024-12-28T07:16:56.708Z
tags: virtualization, virtualbox, labs
editor: markdown
dateCreated: 2024-12-28T07:11:46.927Z
---

## Pendahuluan
Share folder dari windows 10 ke ubuntu desktop dengan GUI relatif mudah dilakukan. Cukup dengan menambahkan entry shared folder di menu settings VM di virtualbox, maka share folder biasanya akan langsung muncul di file explorer Ubuntu. Tapi bagaimana dengan share folder ke ubuntu server yang biasanya tanpa GUI? Berikut langkah-langkahnya.

## Prasyarat
- Virtualbox yang telah terinstall di Windows 10 beserta extension pack.
- Ubuntu server yang telah terinstall di Virtualbox.

## Step 1 - Konfigurasi di Virtualbox
- Masuk ke settings
- Klik tab "Shared Folders"

    ![share-folder-ubuntu-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-1.png "share-folder-ubuntu-1")

- Klik "Adds new shared folder" di sebelah kanan

    ![share-folder-ubuntu-2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-2.png "share-folder-ubuntu-2")

- Pilih folder di host yang akan di share pada bagian "Folder Path". Isi nama share pada "Folder Name". Klik OK.

    ![share-folder-ubuntu-3](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-3.png "share-folder-ubuntu-3")

- Entry shared folder akan muncul. Klik OK.

    ![share-folder-ubuntu-4](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-4.png "share-folder-ubuntu-4")

- Nyalakan VM. Jika sudah up, klik menu "Devices", lalu klik "Insert Guest Additions CD Image…"

    ![share-folder-ubuntu-5](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-5.png "share-folder-ubuntu-5")

## Step 2 - Konfigurasi di Ubuntu
- Mount "guest addition cd image" yang telah di-Insert.
    ```
    $ sudo mkdir /media/cdrom
    $ sudo mount /dev/sr0 /media/cdrom
    ```
- Jalankan script VboxLinuxAdditions
`$ sudo ./VBoxLinuxAdditions.run`

    ![share-folder-ubuntu-6](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-6.png "share-folder-ubuntu-6")

- Restart vm
    ```
    $ sudo reboot
    ```
- Jika sudah up kembali, test mount shared folder secara manual, gunakan tipe file system "vboxsf". Terserah mau mount dimana, pada contoh ini, share folder akan di mount ke /home/ubuntu/hosts.
    ```
    $ mkdir /home/ubuntu/hosts
    $ sudo mount -t vboxsf vm-share /home/ubuntu/hosts
    ```
Note: "vm-share" merupakan nama share yang telah dibuat di virtualbox settings.
- Cek apakah sudah di-mount
    ```$ mount | grep vm-share```

    ![share-folder-ubuntu-7](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-7.png "share-folder-ubuntu-7")

- Jika sudah berhasil, supaya shared foldernya bisa mount secara otomatis ketika vm ubuntu-nya dinyalakan, tambahkan entry mount shared folder tersebut ke /etc/fstab.
    ```
    $ sudo vim /etc/fstab
    vm-share /home/ubuntu/hosts vboxsf defaults 0 0
    ```

    ![share-folder-ubuntu-8](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-ubuntu-8.png "share-folder-ubuntu-8")

- Jika sudah, test dengan melakukan reboot. Shared folder tersebut akan mount secara otomatis ketika booting, Insya Allah.

## Penutup
Share folder dari Windows 10 ke Ubuntu Server VM tanpa GUI ternyata cukup mudah dilakukan. Kita bisa dengan cepat memindahkan file dari maupun ke dalam VM melalui fitur share folder yang disediakan oleh Virtualbox.