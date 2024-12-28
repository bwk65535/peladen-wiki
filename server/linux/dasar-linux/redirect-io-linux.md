---
title: Redirect I/O ke file atau program
description: 
published: true
date: 2024-12-28T10:51:17.325Z
tags: server, linux
editor: markdown
dateCreated: 2024-12-28T10:51:17.325Z
---

Sebuah *process* atau *program* yang sedang berjalan butuh untuk mengambil imput dan mengirim output dari/ke suatu tempat. Contohnya *command* yang dijalankan di *shell* membaca input dari ketikan keyboard di terminal dan mengeluarkan outputnya di layar terminal.

Input dan output di dapat oleh suatu *process* melalui channel yang disebut juga **file descriptors**. Ketika sebuah process berjalan, dia akan mendapatkan paling tidak tiga file descriptors, yaitu:
- **standard input** (channel 0), membaca input dari keyboard.
- **standard output** (channel 1), mengirim output ke terminal.
- **standard error** (channel 2), mengirim pesan *error* ke terminal.

![file descriptors](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/stdinouterr.png "file descriptors")

| Nomor | Channel | Keterangan | Default Connection | Penggunaan |
| ----- | ------- | ---------- | ------------------ | ---------- |
| 0 | stdin | standard input | keyboard | read only |
| 1 | stdout | standard output | terminal | write only |
| 2 | stderr | standard error | terminal | write only |
| 3+ | *filename* | File lainnya | none | read and/or write |

## Redirecting output ke file
Kita bisa mengubah default connection dari file descriptors di atas untuk merubah input/outputnya ke file. Hal ini di namakan juga *I/O redirection*. Misalnya kita ingin mengubah output stdout ke file, maka output tersebut tidak akan muncul di terminal melainkan akan ditulis ke dalam file.

Perlu diingat apabila kita mengubah stdout seperti contoh di atas, maka stderr tetap akan dikirim ke terminal karena berbeda file descriptor. Kita juga bisa men-*discard* stderr/stdout sehingga tidak tampil di manapun dengan me-*redirect* ke *special file* `/dev/null`.

Berikut beberapa operator untuk melakukan redirect:

| Command | Penjelasan |
| ------- | ---------- |
| > file | redirect stdout ke file (overwrite) |
| >> file | redirect stdout ke file (apppend) |
| 2> file | redirect stderr ke file (overwrite) |
| 2>> file | redirect stderr ke file (apppend) |
| 2> /dev/null | discard stderr messages |
| > file 2>&1 | redirect stdout dan stder ke file (overwrite) |
| &> file | idem |
| >> file 2>&1 | redirect stdout dan stder ke file (append) |
| &>> file | idem |

Beberapa contoh penggunaan redirect:
- Menyimpan tanggal sekarang ke file <br />
`$ date > timestamp`
- Copy 20 line terakhir dari log file ke file yang lain <br />
`$ tail -n 20 /var/log/messages > last-20-log-messages`
- Gabungkan beberapa file menjadi satu <br />
`$ cat file1 file2 file3 > fileall`
- Append output ke sebuah file <br />
`# echo "nameserver 1.1.1.1" >> /etc/hosts`

## Redirecting input menggunakan *pipe*
Sebuah pipeline adalah urutan dari satu atau lebih *command* yang dipisahkan oleh karakter **pipe** `|`. Pipe digunakan untuk menghubungkan stdout dari command sebelumnya menjadi stdin dari command setelahnya.

![redirect with pipe](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/redirectwithpipe.png "redirect with pipe")

Pipeline umumnya digunakan untuk memformat/memfilter output dari suatu command sebelum ditampilkan di terminal. Perbedaannya pipeline dengan I/O redirection adalah pipeline memanipulasi stdout dan stdin antar proses, sedangkan I/O redirection memanipulasi stdout/stdin/stderr ke file.

Berikut beberapa contoh penggunaan pipeline:
- Hanya menampilkan 5 hasil teratas command ls
    ```
    $ ls -l / | head -n 5
    total 16
    lrwxrwxrwx.   1 root root    7 Nov  3  2020 bin -> usr/bin
    dr-xr-xr-x.   5 root root 4096 Apr  7 14:07 boot
    drwxr-xr-x.  18 root root 2900 May 29 02:28 dev
    drwxr-xr-x.  81 root root 8192 May 29 02:28 etc
    ```
- Menghitung berapa baris tulisan dalam sebuah file
    ```
    $ cat /etc/hosts | wc -l
    5
    ```
- Memfilter command `ip address` menjadi hanya menampilkan ip dan devicenya.
    ```
    $ ip address | grep inet
        inet 127.0.0.1/8 scope host lo
        inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
    ```

## Menggunakan command **tee**
Jika kita ingin melakukan redirect ke file dan sekaligus menggunakan pipeline, biasanya hasilnya tidak seperti yang diinginkan karena *shell* akan membuang stdout dari command ke file sebelum stdout itu masuk ke pipeline. Sehingga tidak ada output yang dihasilkan dari penggunaan pipeline seperti contoh di bawah.
```
$ ip address > ipkomputer.txt | grep inet
$
```

Kita bisa menggunakan command **tee** untuk melakukan hal semacam ini. tee akan mengkopi stdin ke stdout dan juga melakukan redirect ke file sekaligus.

![tee command](https://res.cloudinary.com/peladen/image/upload/v1612739828/peladen/2021/05/tee.png "tee command")

Berikut beberapa contoh penggunaan tee:
- Redirect ke file sekaligus menampilkan di terminal
    ```
    $ ls -l / | tee root_dir.txt
    total 16
    lrwxrwxrwx.   1 root root    7 Nov  3  2020 bin -> usr/bin
    dr-xr-xr-x.   5 root root 4096 Apr  7 14:07 boot
    drwxr-xr-x.  18 root root 2900 May 29 02:28 dev
    drwxr-xr-x.  81 root root 8192 May 29 02:28 etc
    drwxr-xr-x.   3 root root   21 Apr  7 13:49 home
    lrwxrwxrwx.   1 root root    7 Nov  3  2020 lib -> usr/lib
    lrwxrwxrwx.   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
    drwxr-xr-x.   2 root root    6 Nov  3  2020 media
    drwxr-xr-x.   3 root root   24 Apr  7 13:58 mnt
    drwxr-xr-x.   3 root root   39 Apr  7 13:58 opt
    dr-xr-xr-x. 104 root root    0 May 29 02:28 proc
    dr-xr-x---.   2 root root  107 Apr  7 14:01 root
    drwxr-xr-x.  24 root root  740 May 29 02:28 run
    lrwxrwxrwx.   1 root root    8 Nov  3  2020 sbin -> usr/sbin
    drwxr-xr-x.   2 root root    6 Nov  3  2020 srv
    dr-xr-xr-x.  13 root root    0 May 29 02:28 sys
    drwxrwxrwt.   8 root root  172 May 29 03:31 tmp
    drwxr-xr-x.  12 root root  144 Apr  7 13:45 usr
    drwxr-xr-x.  20 root root  278 Apr  7 20:50 var
    ```
- Redirect ke file lalu menampilkan di terminal sesuai filter
    ```
    $ ip address | tee ipkomputer.txt | grep inet
        inet 127.0.0.1/8 scope host lo
        inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
    ```
- Redirect ke file yang membutuhkan sudo privilege
    ```
    $ cat /proc/sys/net/ipv4/ip_forward
    0
    $ echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
    1
    $ cat /proc/sys/net/ipv4/ip_forward
    1
    ```

### Catatan
stderr dapat di-redirect menggunakan pipe, tetapi tidak dapat dilakukan menggunakan *merging redirection* (&> dan &>>). Kita dapat menggunakan operator `2>&1` untuk melakukan hal tersebut.
Contoh redirect stdout dan stderr dari command `du` ke command `less`:

`$ du -h --max-depth=1 / 2>&1 | less`