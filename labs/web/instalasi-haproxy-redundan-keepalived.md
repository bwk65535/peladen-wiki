---
title: Instalasi HAProxy Redundan dengan Keepalived
description: 
published: true
date: 2025-01-02T08:22:47.925Z
tags: labs, web
editor: markdown
dateCreated: 2025-01-02T08:22:47.925Z
---

Load balancing adalah salah satu metode untuk mendistribusikan trafik ke beberapa tujuan (*upstream*). Perangkat yang melakukan load balancing disebut sebagai **Load Balancer**. Load balancer berguna jika kita ingin membuat service/server kita *highly available* dengan cara membuat lebih dari satu instance server, lalu trafik dari client kita bagi ke beberapa server tersebut melalui load balancer. Load balancer dapat mendeteksi jika ada server di belakangnya yang mati dan mengalihkan trafik ke server yang masih menyala.

[HAProxy](https://haproxy.org/) adalah salah satu load balancer TCP/HTTP yang populer saat ini. HAProxy merupakan software open source, terkenal sangat cepat dan efisien dalam penggunaannya. Untuk membuat HAProxy  redundan kita akan membuat 2 instance HAProxy, dan menggunakan [Keepalived](https://keepalived.org/) yang berfungsi mendeteksi jika instance HAProxy utama *down* lalu melakukan *failover* ke instance backupnya. Keepalived menggunakan Virtual Redundancy Routing Protocol (VRRP) untuk failovernya dan membutuhkan virtual ip address yang akan aktif pada instance primary.

## Prasyarat
- Niat.
- 4 mesin yang sudah terinstall OS. Di lab ini akan menggunakan AlmaLinux.
  - 2 mesin untuk haproxy.
  - 2 mesin untuk contoh server upstream.
- Hostname resolvable via DNS. Di lab ini menggunakan [nip.io](https://nip.io/) supaya simpel.
- SELinux dalam kondisi mati di semua server (ngga guna wkwk).

Berikut setup lab yang digunakan:

| hostname | ip address | notes |
|---|---|---|
| haproxy01-192-168-56-101.nip.io | 192.168.56.101 | HAProxy primary |
| haproxy02-192-168-56-102.nip.io | 192.168.56.102 | HAProxy backup |
| lb-192-168.56-100.nip.io | 192.168.56.100 | HAProxy Virtual ip address |
| upstream01-192-168-56-103.nip.io | 192.168.56.103 | web server 1 |
| upstream02-192-168-56-104.nip.io | 192.168.56.104 | web server 2 |

Dan 2 contoh alamat web yang akan diakses:
- web01-192-168-56-100.nip.io
- web02-192-168-56-100.nip.io

Note: jika mau ngelab menggunakan virtualbox+vagrant, bisa menggunakan contoh [Vagrantfile](https://gist.github.com/bwk65535/70ba078acf1546f971a1beb3439448be) berikut.

![topologi haproxy](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen//2022/06/haproxy-redundant.png "topologi haproxy")

## Step 1 - Konfigurasi Firewall
Open port-port berikut di **kedua server HAProxy**:
- tcp 80 - http
- tcp 443 - https
- tcp 8080 - HAProxy stats
- VRRP control traffic

Contoh menggunakan firewalld:
```
$ sudo firewall-cmd --permanent --add-port=80/tcp
$ sudo firewall-cmd --permanent --add-port=443/tcp
$ sudo firewall-cmd --permanent --add-port=8080/tcp
$ sudo firewall-cmd --permanent --add-rich-rule='rule protocol value="vrrp" accept'
$ sudo firewall-cmd --reload
$ sudo firewall-cmd --list-all
```

Open port http di **kedua server upstream**:
```
$ sudo firewall-cmd --permanent --add-service=http
$ sudo firewall-cmd --reload
$ sudo firewall-cmd --list-all
```

## Step 2 - Instalasi dan konfigurasi Keepalived
Note: lakukan lagkah-langkah berikut di **kedua server HAProxy**.
```
$ sudo dnf -y upgrade
$ sudo dnf -y install keepalived
```

### Set kernel parameter
Virtual ip address HAProxy (192.168.56.100) akan di handle oleh VRRP node yang sedang aktif (primary), dan tidak ada di VRRP node backup. Sehingga perlu enable kernel parameter **nonlocal_bind** supaya HAProxy backup dapat bind ke Virtual ip address.
```
$ echo "net.ipv4.ip_nonlocal_bind=1" | sudo tee /etc/sysctl.d/ip_nonlocal_bind.conf
$ sudo sysctl -p
```

### Konfigurasi Keepalived
Backup konfigurasi bawaan keepalived dan gunakan konfigurasi berikut untuk masing-masing server haproxy:
```
$ sudo mv /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.ori
$ sudo vi /etc/keepalived/keepalived.conf
```

Konfigurasi untuk haproxy01 (primary):
```
global_defs {
   notification_email {
     root@localhost
   }
   notification_email_from root@localhost
   smtp_server localhost
   smtp_connect_timeout 30
}

vrrp_script check_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}

vrrp_instance VI_HAPROXY {
    state MASTER
    interface enp0s8 
    virtual_router_id 255
    priority 101
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass NzUwYmY1Y2FhM2IzMjE1OThiNjcyYzFl
    }
    virtual_ipaddress {
        192.168.56.100
    }
    
    track_script {
        check_haproxy
    }
}
```

Konfigurasi untuk haproxy02 (backup):
```
global_defs {
   notification_email {
     root@localhost
   }
   notification_email_from root@localhost
   smtp_server localhost
   smtp_connect_timeout 30
}

vrrp_script check_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}

vrrp_instance VI_HAPROXY {
    state BACKUP
    interface enp0s8 
    virtual_router_id 255
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass NzUwYmY1Y2FhM2IzMjE1OThiNjcyYzFl
    }
    virtual_ipaddress {
        192.168.56.100
    }
    track_script {
        check_haproxy
    }
}
```

#### Penjelasan singkat:
```
vrrp_script check_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}
```
Bagian ini merupakan script untuk mengecek apakah process "haproxy" masih aktif atau tidak degan interval 2 detik. Jika haproxy mati maka nilai prioritas pada vrrp instance akan berkurang 2 sesuai jumlah weight.

```
vrrp_instance VI_HAPROXY {
...
}
```
Membuat instance vrrp dengan nama VI_HAPROXY, di dalam satu server kita bisa membuat lebih dari satu instance.

`state MASTER`: state dari server tersebut, bisa berupa MASTER atau BACKUP.

`interface enp0s8`: set interface yang akan diaktifkan vrrp, harus satu subnet dengan server pasangannya.

`authentication { ... }`: set authentikasi vrrp antar server.

`virtual_ipaddress`{ ... }: set virtual ip address yang akan dipakai oleh instance master. Jika masternya down, virtual ip ini otomatis akan berpindah ke instance backup.

`track_script { ... }`: set cara pengecekan state vrrp, di contoh ini menggunakan script "check_haproxy" seperti yang sudah dijelaskan di atas.

### Enable dan start service keepalived
`$ sudo systemctl enable --now keepalived`

## Step 3 - Instalasi dan konfigurasi HAProxy
Note: lakukan langkah-langkah berikut di **kedua server HAProxy**.

### Install package HAProxy

`$ sudo dnf -y install haproxy`

### Konfigurasi HAProxy
Backup konfigurasi bawaan haproxy di `/etc/haproxy/haproxy.cfg`, lalu gunakan konfigurasi di bawah ini:

```
$ sudo mv /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.cfg.ori
$ sudo vi /etc/haproxy/haproxy.cfg
```
```
global
    log         127.0.0.1 local2
    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon
    stats socket /var/lib/haproxy/stats level admin
    ssl-default-bind-ciphers TLSv1.2
    ssl-default-server-ciphers intermediate

defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 3000

frontend www
    bind 192.168.56.100:80
    mode http

    acl web01_url req.hdr(host) web01-192-168-56-100.nip.io
    acl web02_url req.hdr(host) web02-192-168-56-100.nip.io
    use_backend web01 if web01_url
    use_backend web02 if web02_url

backend web01
    balance roundrobin
    server upstream01 192.168.56.103:80 check
    server upstream02 192.168.56.104:80 check

backend web02
    balance roundrobin
    server upstream01 192.168.56.103:80 check
    server upstream02 192.168.56.104:80 check

listen stats
    bind 192.168.56.100:8080
    mode http
    stats enable
    stats uri /
    stats realm HAProxy\ Statistics
    stats auth admin:haproxy
```

#### Penjelasan singkat:
```
backend web01
    balance roundrobin
    server upstream01 192.168.56.103:80 check
    server upstream02 192.168.56.104:80 check
```
Konfigurasi server upstream, disini ada 2 server upstream dan di load balance menggunakan algoritma round robin.

```
frontend www
    bind 192.168.56.100:80
    mode http

    acl web01_url req.hdr(host) web01-192-168-56-100.nip.io
    acl web02_url req.hdr(host) web02-192-168-56-100.nip.io
    use_backend web01 if web01_url
    use_backend web02 if web02_url
```
Membuka port http dengan virtual ip address yang telah dikonfigurasi. Client akan mengirim request ke arah vip dan port ini lalu akan diteruskan ke upstream sesuai domainnya. Misalkan domain yang dituju adalah web01-192-168-56-100.nip.io, maka akan menggunakan backend web01 sesuai yang dikonfigurasi pada bagian backend.

### Enable dan start service HAProxy
`$ sudo systemctl enable --now haproxy`

## Step 4 - Instalasi dan konfigurasi web server
Untuk testing, kita akan menggunakan Nginx web server sebagai upstream.
Jalankan step-step berikut di web server 1 dan 2.

### Install Nginx
```
$ sudo dnf -y upgrade
$ sudo dnf -y install nginx
```

### Konfigurasi Nginx
Buat virtual host untuk 2 alamat web berikut.
```
$ sudo vi /etc/nginx/conf.d/web01.conf
server {
    listen       80;
    server_name  web01-192-168-56-100.nip.io www.web01-192-168-56-100.nip.io;

    location / {
        root   /usr/share/nginx/web01;
        index  index.html index.htm;
    }
}
```
```
$ sudo vi /etc/nginx/conf.d/web02.conf
server {
    listen       80;
    server_name  web02-192-168-56-100.nip.io www.web02-192-168-56-100.nip.io;

    location / {
        root   /usr/share/nginx/web02;
        index  index.html index.htm;
    }
}
```

Buat direktori root untuk kedua web, dan isi test page.
```
$ sudo mkdir /usr/share/nginx/web01 /usr/share/nginx/web02
$ echo "WEB 01 - TEST PAGE" | sudo tee /usr/share/nginx/web01/index.html
$ echo "WEB 02 - TEST PAGE" | sudo tee /usr/share/nginx/web02/index.html
```

### Enable dan start service Nginx
`$ sudo systemctl enable --now nginx`

## Step 5 - Akses web
Kedua alamat web di atas seharusnya sudah bisa diakses dan akan di load balance oleh haproxy ke 2 upstream server di belakangnya.
```
$ curl -i http://web01-192-168-56-100.nip.io/
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Mon, 27 Jun 2022 08:00:49 GMT
Content-Type: text/html
Content-Length: 19
Last-Modified: Sun, 26 Jun 2022 11:59:02 GMT
ETag: "62b84a06-13"
Accept-Ranges: bytes

WEB 01 - TEST PAGE

$ curl -i http://web02-192-168-56-100.nip.io/
HTTP/1.1 200 OK
Server: nginx/1.14.1
Date: Mon, 27 Jun 2022 08:00:57 GMT
Content-Type: text/html
Content-Length: 19
Last-Modified: Sun, 26 Jun 2022 11:59:07 GMT
ETag: "62b84a0b-13"
Accept-Ranges: bytes

WEB 02 - TEST PAGE
```
Kita bisa mengecek HAProxy stats dan status upstreamnya di halaman stats pada virtual ip port 8080 sesuai yang telah di konfigurasi.

![haproxy stats](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2022/06/haproxy_stats.png "haproxy stats")
