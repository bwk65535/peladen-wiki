---
title: Membuat dan Menjalankan Ansible Playbook
description: 
published: true
date: 2025-01-01T14:04:34.585Z
tags: automation, ansible
editor: markdown
dateCreated: 2025-01-01T14:02:19.798Z
---

Ansible dapat dijalankan menggunakan adhoc command untuk mengeksekusi one-time command dengan cepat. Akan tetapi, fitur unggulan ansible sesungguhnya terletak pada **playbook**. Ansible playbook dapat digunakan untuk menjalankan multiple tasks untuk banyak managed hosts sekaligus secara identik dan repeatable.

Di dalam playbook, ada beberapa task yang disebut **play**. Di dalam satu play ada satu atau lebih **task** yang harus dijalankan oleh ansible. Play di dalam playbook dieksekusi secara berurutan/sekuensial. Dengan adanya playbook ini, tasks atau tugas-tugas yang ingin kita kerjakan dapat disimpan di dalam bentuk yang dapat dieksekusi berulang kali sekaligus mudah dipahami oleh manusia.

## Format Ansible Playbook
Kita ambil contoh command ansible adhoc berikut:
```
$ ansible -m user -a 'name=eriksen uid=6000 state=present' web1.example.com
```

Adhoc command tersebut dapat kita konversi menjadi play dan kita simpan di dalam playbook seperti berikut:
```
$ cat add_user.yml
---
- name: Konfigurasi user spesifik
  hosts: web1.example.com
  tasks:
    - name: user eriksen dengan uid 6000
      user:
        name: eriksen
        uid: 6000
        state: present
```

Pada contoh di atas, playbook ditulis menggunakan format **YAML** dengan ekstensi file **.yml**. File dengan format YAML menggunakan indentasi dengan spasi untuk menunjukkan struktur dari data di dalamnya. Tidak ada ketentuan khusus untuk jumlah spasi yang harus diberikan, akan tetapi YAML mensyaratkan dua ketentuan berikut:
- Data dalam level yang sama harus mempunyai indentasi/jumlah spasi yang sama.
- Item yang merupakan child/turunan dari item di atasnya harus menggunakan indentasi/jumlah spasi yang lebih banyak dari item parent/atasnya.

Sebagai catatan, YAML hanya menggunakan karakter spasi sebagai indentasi dan tidak menggunakan karakter tab.

### Penjelasan Playbook
Line pertama playbook umumnya ditandai dengan tiga dashes (---) sebagai penanda awal dokumen YAML dan bisa diakhiri dengan tiga dots (...), tetapi biasanya tiga dots ini tidak dituliskan.

Di dalam playbook ada satu atau beberapa "play". Play merupakan kumpulan dari key-value pairs yang merupakan isi play. Dari contoh play di atas, bisa kita lihat isi dari play tersebut terdiri dari 3 keys:
```
- name: Konfigurasi user spesifik
```
Key `name` yang menunjukkan nama dari play
```
  hosts: web1.example.com
```
key `hosts` yang menunjukkan Managed host yang merupakan target dari play
```
  tasks:
    - name: user eriksen dengan uid 6000
      user:
        name: eriksen
        uid: 6000
        state: present
```
Serta key `tasks` yang menunjukkan satu atau beberapa tasks yang harus dijalankan untuk mencapai fungsi dari play ini. Karena bisa ada lebh dari satu task, maka key ini merupakan sebuah list.

Key `name`, `hosts`, dan `tasks` berada pada indentasi yang sama karena mereka satu level. Key `tasks` mempunyai beberapa children seperti `name` dan `user`, dan key `user` mempunyai beberapa children lagi yang menunjukkan spesifikasinya.

Play dan task akan di eksekusi sesuai urutan di playbook, karena itu penempatan play dan task penting untuk diperhatikan.

## Menjalankan playbook
Playbook dapat dijalankan menggunakan command `ansible-playbook` diikuti nama file playbook sebagai argument.
```
$ ansible-playbook nama_file.yml
```
Kita coba jalankan contoh playbook di atas untuk menambahkan user. Karena menambah user perlu permission sudo, maka kita tambahkan parameter `-b` atau `--become` yang fungsinya untuk merubah user.
```
$ ansible-playbook add_user.yml -b

PLAY [Konfigurasi user spesifik] ***************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [web1.example.com]

TASK [user eriksen dengan uid 6000] ************************************************************************************
changed: [web1.example.com]

PLAY RECAP *************************************************************************************************************
web1.example.com           : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Di atas dapat kita lihat nama play yang dijalankan, nama task-task yang dijalankan beserta hasilnya. Pada bagian PLAY RECAP dapat kita lihat juga hasil keseluruhan dari playbook. Status `changed=1` menunjukkan perubahan yang dilakukan pada managed host yaitu pada task menambahkan user eriksen.

Kita bisa melihat apakah user tersebut sudah ada di host `web1.example.com` menggunakan ansible adhoc command:
```
$ ansible web1.example.com -m shell -a 'id eriksen; grep eriksen /etc/passwd'
web1.example.com | CHANGED | rc=0 >>
uid=6000(eriksen) gid=6000(eriksen) groups=6000(eriksen)
eriksen:x:6000:6000::/home/eriksen:/bin/bash
```

Untuk membuktikan bahwa playbook itu idempotent, kita bisa jalankan playbook di atas sekali lagi:
```
$ ansible-playbook add_user.yml -b

PLAY [Konfigurasi user spesifik] ***************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [web1.example.com]

TASK [user eriksen dengan uid 6000] ************************************************************************************
ok: [web1.example.com]

PLAY RECAP *************************************************************************************************************
web1.example.com           : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Di sini terlihat bagian `changed=0` menunjukkan tidak ada perubahan karena user yang diinginkan sudah exist sehingga task tambah user tersebut tidak di eksekusi kembali.

### Meningkatkan detail output (verbosity)
Tambahkan parameter `-v` untuk menambahkan verbosity dari output command. Kita bisa menambah hingga 4 v untuk mendapatkan level verbosity yang berbeda.

| Opsi | Output |
| --- | --- |
| -v | menampilkan task result |
| -vv | menampilkan task result dan task configuration |
| -vvv | menambah detail koneksi ke managed host |
| -vvvv | menambah detail koneksi yang lebih lengkap |

### Verifikasi syntax
Kita bisa mengecek apakah playbook yang dibuat sudah valid secara syntax menggunakan parameter `--syntax-check`.
```
$ ansible-playbook --syntax-check add_user.yml

playbook: add_user.yml
```
Jika playbook tersebut tidak ada syntax error maka tidak menampilkan apa-apa. Tetapi jika ada error, maka akan ditunjukkan jenis dan letak syntax error tersebut pada playbook.
```
$ ansible-playbook --syntax-check add_user.yml
ERROR! We were unable to read either as JSON nor YAML, these are the errors we got from each:
JSON: Expecting value: line 1 column 1 (char 0)

Syntax Error while loading YAML.
  mapping values are not allowed in this context

The error appears to be in '/home/vagrant/ans1/add_user.yml': line 8, column 12, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

       name: eriksen
        uid: 6000
           ^ here
```
Contoh di atas line `name` dan `uid` tidak berada pada indentasi yang sama, sehingga file playbook tersebut menjadi tidak valid.

### Dry run playbook
Kita bisa mengecek hal apa saja yang akan dieksekusi playbook sebelum benar-benar dijalankan (dry run) menggunakan opsi `-C`.
```
$ ansible-playbook -C add_user.yml

PLAY [Konfigurasi user spesifik] ***************************************************************************************

TASK [Gathering Facts] *************************************************************************************************
ok: [web2.example.com]

TASK [user eriksen dengan uid 6000] ************************************************************************************
changed: [web2.example.com]

PLAY RECAP *************************************************************************************************************
web2.example.com           : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Pada contoh di atas, ansible melakukan dry run pada playbook tersebut dan menunjukkan apa saja changes yang akan dilakukan, tetapi tidak benar-benar menjalankan playbook tersebut.
```
$ ansible web2.example.com -m shell -a 'id eriksen; grep eriksen /etc/passwd'
web2.example.com | FAILED | rc=1 >>
id: ‘eriksen’: no such usernon-zero return code
```
Apabila kita cek user eriksen masih belum ada di host `web2.example.com` karena kita baru melakukan dry run saja.