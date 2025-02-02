---
title: Instalasi dan Upgrade package Apache Tomcat pada Rocky Linux 9
description: 
published: true
date: 2025-02-02T07:38:52.081Z
tags: labs, server, linux, tomcat
editor: markdown
dateCreated: 2025-02-02T05:14:30.442Z
---

Apache Tomcat adalah sebuah software open source untuk menjalankan program Java di dalam lingkungan web server. Tomcat merupakan implementasi dari teknologi Jakarta Servlet, Jakarta Expression Language, and WebSocket sehingga disebut juga Java Web Application Server.

Tomcat 10 dan sesudahnya mengimplementasikan spesifikasi yang dikembangkan oleh Jakarta EE, sedangkan Tomcat 9 dan sebelumnya mengimplementasikan spesifikasi yang dikembangkan oleh Java EE. Jakarta EE sendiri adalah pengembangan terbaru dari platform Java EE.

## Prasyarat

-   Niat
-   Server yang sudah diinstal Rocky Linux 9

## Instalasi Apache Tomcat

Install Java JDK/JRE sesuai versi yang dibutuhkan.

```plaintext
# dnf install java-11-openjdk.x86_64
```

Prepare user tomcat, supaya tidak perlu menggunakan user root.

```plaintext
# useradd -m -d /opt/tomcat -U -s /bin/false tomcat
```

Download dan konfigurasi package Tomcat.

```plaintext
# wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.31/bin/apache-tomcat-10.1.31.tar.gz -P /tmp
# tar xzvf /tmp/apache-tomcat-10.1.31.tar.gz -C /opt/tomcat --strip-components=1
# chown -R tomcat:tomcat /opt/tomcat/
# chmod -R u+x /opt/tomcat/bin
```

Selanjutnya buat systemd service untuk memudahkan kita me-manage start/stop tomcat.

Pertama, cek path Java JDK/JRE yang sudah terinstall

```plaintext
# alternatives --list
libnssckbi.so.x86_64    auto    /usr/lib64/pkcs11/p11-kit-trust.so
soelim                  auto    /usr/bin/soelim.groff
iptables                auto    /usr/sbin/iptables-nft
ebtables                auto    /usr/sbin/ebtables-nft
arptables               auto    /usr/sbin/arptables-nft
cifs-idmap-plugin       auto    /usr/lib64/cifs-utils/cifs_idmap_sss.so
ld                      auto    /usr/bin/ld.bfd
man                     auto    /usr/bin/man.man-db
man.7.gz                auto    /usr/share/man/man7/man.man-pages.7.gz
mta                     auto    /usr/sbin/sendmail.postfix
java                    auto    /usr/lib/jvm/java-11-openjdk-11.0.25.0.9-3.el9.x86_64/bin/java
jre_openjdk             auto    /usr/lib/jvm/java-11-openjdk-11.0.25.0.9-3.el9.x86_64
jre_11                  auto    /usr/lib/jvm/java-11-openjdk-11.0.25.0.9-3.el9.x86_64
jre_11_openjdk          auto    /usr/lib/jvm/jre-11-openjdk-11.0.25.0.9-3.el9.x86_64
```

Pada output di atas, terlihat jre\_openjdk terletak di path `/usr/lib/jvm/java-11-openjdk-11.0.25.0.9-3.el9.x86_64`

Lalu buat file systemd service untuk tomcat.

-   Sesuaikan value `JAVA_HOME` dengan path jre\_openjdk di atas.
-   Sesuaikan juga value `CATALINA_OPTS` dengan spesifikasi aplikasi java yang ingin dijalankan.

```plaintext
# vi /etc/systemd/system/tomcat.service
[Unit]

Description=Tomcat
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/jre-11"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

Setelah ini, kita bisa memasukkan program Java yang ingin dijalankan ke dalam folder `$CATALINA_HOME/webapps/`.

Jika diperlukan, lalukan penyesuaian pada file `$CATALINA_HOME/conf/server.xml` sesuai ketentuan aplikasi yang ingin dijalankan.

Reload dan jalankan Tomcat

```plaintext
# systemctl daemon-reload
# systemctl start tomcat
# systemctl enable tomcat
```

Cek status tomcat dan port yang listen

```plaintext
# systemctl status tomcat
# netstat -plnt | grep java
```

Cek versi tomcat yang terinstall

```plaintext
java -cp /opt/tomcat/lib/catalina.jar org.apache.catalina.util.ServerInfo
```

## Upgrade Apache Tomcat

Untuk melakukan upgrade tomcat, langkah-langkahnya kurang lebih sama seperti saat melakukan instalasi.

Matikan tomcat terlebih dahulu, lalu backup folder tomcat yang lama.

```plaintext
# systemctl stop tomcat
# cp -rp /opt/tomcat /opt/tomcat-old
```

Download dan install Tomcat versi baru.

```plaintext
# wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.34/bin/apache-tomcat-10.1.34.tar.gz -P /tmp
# tar xzvf /tmp/apache-tomcat-10.1.34.tar.gz -C /opt/tomcat --strip-components=1
# chown -R tomcat:tomcat /opt/tomcat/
# chmod -R u+x /opt/tomcat/bin
```

Jalankan kembali tomcat, dan cek status serta versi yang telah di upgrade.

```plaintext
# systemctl start tomcat
# systemctl status tomcat

# netstat -plnt | grep java
# java -cp /opt/tomcat/lib/catalina.jar org.apache.catalina.util.ServerInfo
```