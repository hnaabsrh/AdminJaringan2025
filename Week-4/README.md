<div align="center">
    <h1 style="text-align: center;font-weight: bold">Ekosistem Internet dan Konsep DNS<br>Workshop Administrasi Jaringan</h1>
    <h4 style="text-align: center;">Dosen Pengampu : Dr. Ferry Astika Saputra, S.T., M.Sc.</h4>
</div>
<br />
<div align="center">
    <img src="Assets/Logo_PENS.png" alt="Logo PENS">
    <h4 style="text-align: center;">Disusun Oleh : </h4>
    <p style="text-align: center;">
        <strong>Roihanah Inayati Bashiroh</strong><br>
        <strong>3123500005 / 2 D3 IT A</strong><br>
    </p>

<h3>Politeknik Elektronika Negeri Surabaya<br>Departemen Teknik
Informatika Dan Komputer<br>Program Studi Teknik Informatika<br>2024/2025</h3>
    <hr>
    <hr>
</div>

## Daftar Isi

- [Ekosistem Internet dan DNS Concept](#ekosistem-internet-dan-dns-concept)
  - [nsswitch](#nsswitch)
  - [Penjelasan Beberapa Layanan](#penjelasan-beberapa-layanan)
  - [File /etc/hosts](#file-etchosts)
  - [Cek IP](#cek-ip)
  - [resolv.conf](#resolvconf)
  - [Test ping www](#test-ping-www)
  - [root.hints](#roothints)
- [Instalasi & Konfigurasi bind9](#instalasi--konfigurasi-bind9)
  - [Konfigurasi Jaringan Internal](#konfigurasi-jaringan-internal)
  - [Konfigurasi dan Penyesuaian BIND](#konfigurasi-dan-penyesuaian-bind)
  - [Konfigurasi Zona DNS di BIND](#konfigurasi-zona-dns-di-bind)
  - [Konfigurasi opsi BIND untuk IPv4](#konfigurasi-opsi-bind-untuk-ipv4)
  - [Konfigurasi Zone Files](#konfigurasi-zone-files)
  - [BIND: Verify Resolution](#bind-verify-resolution)
  - [Konfigurasi DNS Client](#konfigurasi-dns-client)
  - [DNS Query menggunakan DiG](#dns-query-menggunakan-dig)
  - [Reverse DNS Lookup](#reverse-dns-lookup)
- [Referensi](#referensi)

<br>
<br>
<br>


## Ekosistem Internet dan DNS Concept

![App Screenshot](Assets/19.jpg)<br>

### nsswitch

Pada sistem Linux seperti Debian, file `/etc/nsswitch.conf` berfungsi sebagai konfigurasi Name Service Switch (NSS). File ini menentukan mekanisme pencarian informasi dalam sistem, termasuk pencarian pengguna, grup, dan host.

![App Screenshot](Assets/20.jpg)<br>

### Penjelasan Beberapa Layanan

- **passwd, group, dan shadow**: Digunakan untuk mendapatkan informasi terkait pengguna dan grup. Jika opsi **files** digunakan, data akan diambil dari `/etc/passwd`, `/etc/group`, dan `/etc/shadow`.
- **hosts**: Menentukan cara pencarian alamat IP berdasarkan nama host. Jika menggunakan **files**, sistem akan mengecek `/etc/hosts` terlebih dahulu sebelum menggunakan **dns**.
- **networks, protocols, services, ethers, dan rpc**: Berisi informasi jaringan, protokol, layanan, serta konfigurasi lainnya dalam sistem.

### File /etc/hosts

File `/etc/hosts` digunakan untuk melakukan pemetaan nama host ke alamat IP secara lokal tanpa perlu menggunakan layanan DNS. Sistem akan lebih dulu mencari entri dalam file ini sebelum mencoba resolusi domain melalui layanan eksternal.

![App Screenshot](Assets/21.jpg)<br>

Untuk menambahkan pemetaan, buka file dengan hak akses root:

```bash
sudo nano /etc/hosts
```

![App Screenshot](Assets/22.jpg)<br>

Tambahkan entri seperti berikut:

```bash
10.252.108.110    dev
```

![App Screenshot](Assets/23.jpg)<br>

### Cek IP

Untuk mengetahui alamat IP yang digunakan, jalankan perintah berikut:

```bash
ip a
```

![App Screenshot](Assets/24.jpg)<br>

### resolv.conf

File `/etc/resolv.conf` digunakan untuk mengonfigurasi server DNS yang digunakan dalam pencarian nama domain. Isi file dapat diperiksa dengan perintah berikut:

```bash
sudo less /etc/resolv.conf
```

Dalam file ini, **nameserver** menentukan alamat IP server DNS yang digunakan, dan **search** mengatur domain yang akan dicari ketika hanya sebagian nama host yang diberikan.

### Test ping www

Untuk menguji apakah konfigurasi DNS berfungsi, gunakan perintah berikut:

```bash
ping www
```

![App Screenshot](Assets/25.jpg)<br>


### root.hints

File `/usr/share/dns/root.hints` berisi daftar server root DNS yang digunakan oleh resolver rekursif untuk menemukan server otoritatif suatu domain. File ini digunakan oleh server DNS seperti BIND untuk menyelesaikan permintaan yang tidak tersedia dalam cache atau zona lokal.

![App Screenshot](Assets/26.jpg)<br>

![App Screenshot](Assets/27.jpg)<br>



---

## **Instalasi & Konfigurasi bind9**  

### **Pengaturan Jaringan Internal**  

Menginstal bind9  

Jalankan perintah berikut untuk memasang bind9 setelah masuk sebagai root:  

```bash
root@dlp:~# apt -y install bind9 bind9utils
```  

![App Screenshot](Assets/1.jpg)<br>  

### **Menyesuaikan dan Mengonfigurasi BIND**  

Tambahkan file konfigurasi baru `named.conf.internal-zones` ke dalam file utama BIND yang terletak di `/etc/bind/named.conf`.  

![App Screenshot](Assets/3.jpg)<br>  

### **Pengaturan dalam `/etc/bind/named.conf.options`**  

![App Screenshot](Assets/5.jpg)<br>  

Konfigurasi **ACL internal-network** pada jaringan `192.168.80.0/24` menentukan klien mana yang diperbolehkan mengakses layanan DNS. Hanya **localhost** dan jaringan internal yang diberikan izin untuk melakukan query (`allow-query`), sementara transfer zona hanya diizinkan untuk **localhost** (`allow-transfer`). Resolusi rekursif diaktifkan (`recursion yes`) agar bisa mengakses domain eksternal. Selain itu, validasi DNSSEC diatur otomatis (`dnssec-validation auto`), dan BIND dikonfigurasi agar menerima koneksi dari IPv6 (`listen-on-v6 { any; }`).  

### **Menambahkan Zona DNS pada BIND**  

![App Screenshot](Assets/7.jpg)<br>  

**1. Zona Forward (kelompok4.home)**  

Zona ini berfungsi untuk menerjemahkan nama domain menjadi alamat IP dan tersimpan dalam file `/etc/bind/kelompok4.home.lan`. Server ini bertindak sebagai **master (utama)** sehingga menjadi referensi utama untuk data DNS pada domain tersebut. Selain itu, fitur update dinamis tidak diaktifkan dengan konfigurasi `allow-update { none; };`.  

**2. Zona Reverse (80.168.192.in-addr.arpa)**  

Zona ini memiliki fungsi kebalikan dari zona forward, yaitu menerjemahkan alamat IP menjadi nama domain (**reverse lookup**), dan tersimpan dalam file `/etc/bind/80.168.192.db`. Sama seperti zona forward, zona ini juga bertindak sebagai **master**, dengan update dinamis yang tidak diaktifkan.  

### **Mengatur BIND agar Menggunakan Hanya IPv4 dan Menonaktifkan IPv6**  

![App Screenshot](Assets/9.jpg)<br>  

Untuk memastikan BIND hanya bekerja dengan IPv4, buka file `/etc/default/named` dan tambahkan opsi `OPTIONS="-u bind -4"`. Opsi ini memastikan BIND berjalan dengan pengguna `bind` serta hanya menggunakan IPv4, sehingga IPv6 tidak digunakan untuk mencegah potensi kendala jaringan.  

### **Pengaturan File Zona**  

Pengaturan **forward lookup** pada DNS Server BIND untuk jaringan **192.168.80.0/24** dengan domain **kelompok4.home** bertujuan agar server dapat menerjemahkan domain ke IP. File zona dikonfigurasi sebagai **master**, dengan pemetaan nama host yang telah ditentukan.  

![App Screenshot](Assets/11.jpg)<br>  

Pengaturan **reverse lookup** untuk jaringan **192.168.80.0/24** dilakukan dengan membuat file zona `/etc/bind/80.168.192.db`. File ini kemudian didaftarkan sebagai **master** di `/etc/bind/named.conf.local`. Setelah itu, lakukan verifikasi dan restart BIND agar perubahan dapat diterapkan.  

![App Screenshot](Assets/13.jpg)<br>  

### **Memeriksa Resolusi DNS pada BIND**  

Untuk menerapkan perubahan, jalankan perintah berikut guna merestart layanan BIND:  

```bash
root@dlp:~# systemctl restart named
```  

### **Konfigurasi DNS Client agar Menggunakan Server DNS Sendiri**  

![App Screenshot](Assets/16.jpg)<br>  

Sistem Linux menentukan server DNS yang digunakan melalui file `/etc/resolv.conf`. File ini dapat diubah untuk menetapkan DNS server yang dipakai dalam query DNS, seperti menerjemahkan nama domain menjadi IP atau sebaliknya.  

Dengan mengedit **`/etc/resolv.conf`** dan mengatur **nameserver** ke **192.168.80.193**, hal ini berarti:  

- Sistem akan mengutamakan penggunaan **DNS Server internal** (**BIND di 192.168.80.193**).  
- Pencarian DNS akan dilakukan terlebih dahulu di **server lokal** sebelum ke server eksternal.  
- Sistem dapat menyelesaikan **domain internal** yang hanya dikenali oleh DNS lokal.  

### **Melakukan Query DNS Menggunakan DiG untuk Forward Lookup**  

![App Screenshot](Assets/17.jpg)<br>  

Perintah ini digunakan untuk menguji apakah DNS server yang telah dikonfigurasi dapat menyelesaikan nama domain ke alamat IP dengan benar.  

### **Melakukan Reverse DNS Lookup**  

![App Screenshot](Assets/18.jpg)<br>  

Untuk menemukan nama domain yang terhubung dengan suatu alamat IP, gunakan perintah di atas guna melakukan pencarian balik (reverse lookup).


### Referensi
[DNS Concept](https://www.biznetgio.com/news/apa-itu-dns-pengertian-fungsi-cara-kerja-dan-teknologi-anycast-dns)
