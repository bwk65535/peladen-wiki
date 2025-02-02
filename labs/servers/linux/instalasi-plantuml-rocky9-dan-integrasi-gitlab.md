---
title: Instalasi PlantUML pada Rocky Linux 9 dan Integrasi ke Gitlab
description: 
published: true
date: 2025-02-02T07:37:12.005Z
tags: labs, server, linux, gitlab, plantuml
editor: markdown
dateCreated: 2025-02-02T07:28:32.713Z
---

PlantUML merupakan tool yang digunakan untuk merender diagram dari sebuah text. PlantUML dapat diintegrasikan dengan tool lain seperti gitlab, sehingga kita dapat membuat diagram dalam bentuk code yang disimpan di repo. Ketika kita membuka file diagram tersebut melalui web, gitlab akan mengirim text diagram tersebut ke PlantUML untuk dilakukan rendering. Sehingga kita bisa melihatnya dalam bentuk gambar diagram.

## Prasyarat
-   Niat
-   Server yang sudah diinstal Rocky Linux 9

Di lab ini, kita menggunakan server yang sama dengan server gitlab untuk memudahkan.

## Step 1 – Instalasi Prerequisites

Kita membutuhkan Java+Tomcat, beserta package graphviz untuk menjalankan PlantUML.

**Instalasi Graphviz**

```plaintext
# dnf install graphviz
```

**Instalasi Tomcat**

Dapat dicek [disini](/labs/servers/linux/instalasi-dan-upgrade-tomcat-rocky-9)

Setelah selesai diinstal, sesuaikan port tomcat. Defaultnya tomcat listen http menggunakan port 8080, yang biasanya port itu juga digunakan oleh gitlab. Pada contoh ini kita akan mengganti port tomcat menggunakan http port 8005 dan shutdown port 8006

```plaintext
# vi /opt/tomcat/conf/server.xml
```
```plaintext
<Server port="8006" shutdown="SHUTDOWN">
...
   <Connector port="8005" protocol="HTTP/1.1"
...
```

## Step 2 - Instalasi PlantUML

Download package plantuml, lalu ekstrak dan masukkan ke folder webapps tomcat.

```plaintext
# wget -P /tmp https://github.com/plantuml/plantuml-server/releases/download/v1.2024.7/plantuml-jsp-v1.2024.7.war
# cp /tmp/plantuml-jsp-v1.2024.7.war /opt/tomcat/webapps/plantuml.war
# chown tomcat:tomcat /opt/tomcat/webapps/plantuml.war
```

Restart tomcat dan cek port plantuml apakah sudah LISTEN seperti yang dikonfigurasi pada server.xml tomcat.

```plaintext
# systemctl restart tomcat
# systemctl status tomcat
# ss -tlpn | grep java
```

Test curl ke endpoint plantuml atau bisa juga kita open dari browser jika laptop yang kita gunakan bisa langsung mengakses port 8005 ke server.

```plaintext
# curl --location --verbose "http://localhost:8005/plantuml/"
```

![plantuml_server](https://res.cloudinary.com/peladen/image/upload/v1738477803/peladen/2025/02/plantuml_server.png)

## Step 3 – Integrasi PlantUML dengan Gitlab

Konfigurasi plantuml path di gitlab.rb
```plaintext
# vi /etc/gitlab/gitlab.rb
```
Jika bagian `custom_gitlab_server_config` sudah ada isinya, maka masukkan konfigurasi untuk plantuml di satu tempat yg sama. Karena custom\_gitlab\_server\_config pada gitlab.rb hanya bisa satu.

```plaintext
### PlantUML
nginx['custom_gitlab_server_config'] = "
 location /-/plantuml {
   rewrite ^/-/(plantuml.*) /$1 break;
   proxy_set_header  HOST               $host;
   proxy_set_header  X-Forwarded-Host   $host;
   proxy_set_header  X-Forwarded-Proto  $scheme;
   proxy_cache off;
   proxy_pass http://localhost:8005/plantuml;
 }"

gitlab_rails['env'] = { 'PLANTUML_ENCODING' => 'deflate' }
```

Rekonfigurasi gitlab

```plaintext
# gitlab-ctl reconfigure
```

Berikutnya, kita enable PlantUML dari Gitlab Web GUI.

Masuk ke gitlab sebagai admin, lalu Settings > General.

Pada menu general, expand bagian PlantUML

![gitlab_plantuml_settings](https://res.cloudinary.com/peladen/image/upload/v1738477801/peladen/2025/02/gitlab_plantuml_01.png)

Ceklis pilihan Enable PlantUML, lalu isi alamat endpoint plantuml.

Alamat endpoint uml sama dengan domain atau ip address yang digunakan untuk mengakses gitlab ditambah path `/-/plantuml/`

![gitlab_plantuml_enable](https://res.cloudinary.com/peladen/image/upload/v1738477800/peladen/2025/02/gitlab_plantuml_02.png)

## Step 4 – Testing

Untuk pengetesan, kita dapat membuat file yang berisi plantuml block. Beberapa format file yang di support plantuml contohnya markdown, asciidoc, dan reStructuredText.

Sebagai contoh, file markdown berikut akan menampilkan diagram seperti di bawah ini.

\`\`\`plantuml

Bob -> Alice : hello

Alice -> Bob : hi

\`\`\`

![plantuml_markdown_example](https://res.cloudinary.com/peladen/image/upload/v1738477801/peladen/2025/02/plantuml_markdown_example.png)

Ref:
https://docs.gitlab.com/ee/administration/integration/plantuml.html#enable-plantuml-integration

### Troubleshoot

-   Request header is too large

Increase `maxHttpHeaderSize` in `$TOMCAT_HOME/conf/server.xml`

```plaintext
<Connector port="8080" maxHttpHeaderSize="65536" protocol="HTTP/1.1" ... />
```

Ref:
https://www.madhur.co.in/blog/2020/02/29/request-header-is-too-large.html