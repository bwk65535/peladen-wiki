---
title: Share folder Virtualbox dari Windows 10 host ke Centos 8 Server Guest
description: 
published: true
date: 2024-12-28T10:57:43.332Z
tags: virtualbox, labs
editor: markdown
dateCreated: 2024-12-28T07:14:41.815Z
---

## Pendahuluan
Menyambung tulisan tentang share folder dari windows  ke ubuntu, berikutnya kita coba share folder dari windows 10 host ke centos 8 guest. Centos 8 yang diinstall adalah versi minimal yang tanpa GUI.
Berikut langkah-langkahnya.

## Prasyarat
- Virtualbox yang telah terinstall di Windows 10 beserta extension pack.
- Centos 8 minimal yang telah terinstall di Virtualbox.

## Step 1 - Konfigurasi di Virtualbox
- Masuk ke settings
- Klik tab "Shared Folders"

    ![share-folder-centos-1](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-1.png "share-folder-centos-1")

- Klik "Adds new shared folder" di sebelah kanan

    ![share-folder-centos-2](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-2.png "share-folder-centos-2")

- Pilih folder di host yang akan di share pada bagian "Folder Path". Isi nama share pada "Folder Name". Klik OK.

    ![share-folder-centos-3](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-3.png "share-folder-centos-3")

- Entry shared folder akan muncul. Klik OK.

    ![share-folder-centos-4](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-4.png "share-folder-centos-4")

- Nyalakan VM. Jika sudah up, klik menu "Devices", lalu klik "Insert Guest Additions CD Image…"

    ![share-folder-centos-5](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-5.png "share-folder-centos-5")

## Step 2 - Konfigurasi di Centos
- Instal beberapa package yang dibutuhkan untuk menginstall virtualbox linux additions.
    ```
    $ sudo -i
    # dnf install tar bzip2 kernel-devel-$(uname -r) kernel-headers perl gcc make elfutils-libelf-devel
    ```

    ![share-folder-centos-6](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-6.png "share-folder-centos-6")

- Mount "guest addition cd image" yang telah di-Insert.
    ```
    # mkdir /media/cdrom
    # mount /dev/sr0 /media/cdrom
    ```

    ![share-folder-centos-7](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-7.png "share-folder-centos-7")

- Jalankan script VboxLinuxAdditions
    ```
    # ./VBoxLinuxAdditions.run
    ```

    ![share-folder-centos-8](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-8.png "share-folder-centos-8")

- Restart vm
    ```
    # reboot
    ```

- Jika sudah up kembali, test mount shared folder secara manual, gunakan tipe file system "vboxsf". Terserah mau mount dimana, pada contoh ini, share folder akan di mount ke /home/centos/hosts.
    ```
    $ mkdir /home/centos/hosts
    $ sudo mount -t vboxsf shared /home/centos/hosts
    $ mount | grep shared
    ```
    Note: "shared" merupakan nama share yang telah dibuat di virtualbox settings.

    ![share-folder-centos-9](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-9.png "share-folder-centos-9")

- Jika sudah berhasil, supaya shared foldernya bisa mount secara otomatis ketika vm centos-nya dinyalakan, tambahkan entry mount shared folder tersebut ke /etc/fstab.
    ```$ sudo vim /etc/fstab
    vm-share /home/centos/hosts vboxsf defaults 0 0
    ```

    ![share-folder-centos-10](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2020/10/share-folder-centos-10.png "share-folder-centos-10")

- Jika sudah, test dengan melakukan reboot. Shared folder tersebut akan mount secara otomatis ketika booting, Insya Allah.

## Penutup
Sama seperti share folder virtualbox dari Windows 10 ke Ubuntu Server, ternyata share dari Windows 10 ke Centos 8 melalui Virtualbox juga cukup mudah dilakukan. Bedanya, di Centos kita perlu menginstall beberapa package tambahan untuk mengcompile VirtualBox Guest Addition.

### EDIT
Selain mount menggunakan fstab, kita juga bisa mount shared folder menggunakan fitur Auto-mount di virtualbox.

Dengan menggunakan cara tersebut kita tidak perlu membuat direktori untuk mount terlebih dahulu dan tidak perlu juga mengeset entry fstab, karena semua itu sudah di handle oleh virtualbox. Cara ini dapat dilakukan apabila kita juga sudah menginstall VirtualBox Guest addition.