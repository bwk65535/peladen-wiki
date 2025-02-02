---
title: Setup OpenSSH Server di Windows
description: 
published: true
date: 2025-01-02T10:58:55.938Z
tags: labs, windows
editor: markdown
dateCreated: 2025-01-02T10:58:55.938Z
---

SSH merupakan protokol yang banyak digunakan untuk melakukan remote ke suatu server. Pada server linux, ssh biasanya sudah aktif ketika instalasi dengan menggunakan tool OpenSSH Server.

Pada mesin Windows, biasanya kita melakukan remote menggunakan RDP (Remote Desktop Protocol) yang berbasis GUI. Kita akan coba juga mengaktifkan service SSH di mesin Windows supaya kita bisa me-remote mesin windows menggunakan CLI saja menggunakan OpenSSH.

## Prasyarat
- Niat
- Mesin Windows yang akan diaktifkan ssh server.
  - Minimal menggunakan Windows Server 2019 atau Windows 10 build 1809.
  - Mempunyai PowerShell versi 5.1 atau lebih baru.
  - User account yang tergabung di group Adminsitrators.
- Komputer/Laptop lain untuk melakukan test remote

## Step 1 - Instalasi OpenSSH di Windows
Note: Jalankan semua command di bawah menggunakan PowerShell Administrator, bukan PowerShell biasa apalagi CMD.

Cek apakah openssh sudah terinstall
```
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```

Jika belum terinstall, kita bisa menginstall openssh client dan server dengan command berikut:
```
# Install the OpenSSH Client
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Install the OpenSSH Server
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

Nyalakan service openssh server, dan set untuk jalan otomatis ketika start jika diperlukan.
```
# Start the sshd service
Start-Service sshd

# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'
```

## Step 2 - Menghubungkan ke OpenSSH server
Kita bisa melakukan remote ke opnssh server di windows menggunakan user lokal ataupun user online microsoft.
```
# Jika menggunakan user lokal
ssh domain\username@ip_address

# Jika menggunakan user online
ssh email@ip_address
```

## Step 3 - SSH menggunakan keypair
Selain menggunakan password, kita bisa melakukan remote ke ssh server di windows menggunakan ssh keypair seperti di linux.

Ambil public key yang sudah kita generate di sisi client. Lalu masukkan ke file `authorized_keys` di ssh server.
Bedanya jika di Windows, lokasi file authorized_keys untuk user biasa dan user administrator berbeda.

```
# Untuk user biasa
C:\Users\nama_user\.ssh\authorized_keys

# Untuk user admin
C:\ProgramData\ssh\administrators_authorized_keys
```

Selain itu untuk user admin, kita perlu mengubah permission file tersebut suapaya hanya bisa dibaca oleh Administrators.

```
# Jalankan di powershell administrator
icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"
```

## Kesimpulan
OpenSSH server merupakan salah satu software yang memungkinan kita untuk melakaukan remote ke sebuah server berbasis CLI. Selain dijalankan di linux, OpenSSH server juga dapat dijalankan di mesin Windows seperti contoh yang sudah kita lakukan di atas.

Referensi:
- https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell
- https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement