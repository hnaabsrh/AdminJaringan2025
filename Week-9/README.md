<div align="center">
    <h1 style="text-align: center;font-weight: bold">Konfigurasi Bind9 dan Web Server<br>Workshop Administrasi Jaringan</h1>
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
Informatika Dan Komputer<br>Program Studi Teknik Informatika<br>2025/2026</h3>
    <hr>
    <hr>
</div>

---

## Daftar Isi

- [Konfigurasi Bind9](#konfigurasi-bind9)
- [Konfigurasi Web Server (Apache2)](#konfigurasi-web-server-apache2)

---


## Gambar Topologi Jaringan Internal
![App Screenshot](Assets/topologi.jpg)<br>

Sebelum konfigurasi Bind9, lakukan setting network untuk mengatur koneksi jaringan secara manual, bukan otomatis dari DHCP. Ini berguna pada server atau sistem yang memerlukan pengaturan jaringan yang konsisten dan tidak berubah.
![App Screenshot](Assets/networkinterface.jpg)
<br>

Kemudian setting file `/etc/network/interfaces`
![App Screenshot](Assets/setnetwork.jpg)
<br>

Setelah itu lakukan update
![App Screenshot](Assets/updatenetwork.jpg)
<br>
<br>

## Konfigurasi Bind9

### Konfigurasi DNS Server (Bind9) Pada Server
1. Instalasi paket dengan menjalankan perintah `apt -y install bind9 bind9utils`

2. Edit dan modifikasi file `/etc/bind/named.conf`
![App Screenshot](Assets/namedconf.jpg)
<br>

3. Modifikasi file `/etc/bind/named.conf.options`
![App Screenshot](Assets/confoptions.jpg)
<br>

![App Screenshot](Assets/confoptions2.jpg)
<br>

4. Konfigurasi internal zone pada file `/etc/bind/named.conf.internal-zones`
![App Screenshot](Assets/intzones.jpg)
<br>

5. Konfigurasi file `/etc/default/named`
![App Screenshot](Assets/defaultnamed.jpg)
<br>

6. Buat file sesuai dengan domain lokal
![App Screenshot](Assets/domainlokal.jpg)
<br>

7. Buat file sesuai dengan IP Address
![App Screenshot](Assets/ipaddr.jpg)
<br>
<br>

### Tes DNS Server 1
1. Tes DNS Server dari jaringan dalam kelompok
![App Screenshot](Assets/tesdns.jpg)
<br>

![App Screenshot](Assets/tesdns2.jpg)
<br>
<br>

## Konfigurasi Web Server (Apache2)
1. Instalasi paket apache2 Jalankan perintah berikut untuk menginstal Apache2
![App Screenshot](Assets/installapache2.jpg)
<br>

2. Pengaturan Dasar Apache2
    - ServerTokens: Edit dan ubah /etc/apache2/conf-enabled/security.conf
    ![App Screenshot](Assets/confenable.jpg)
    <br>
    Ini digunakan menyembunyikan informasi detail versi Apache pada header HTTP, meningkatkan keamanan.
    <br>

    - DirectoryIndex: Edit /etc/apache2/mods-enabled/dir.conf dan atur urutan file index yang dicari ketika direktori diakses:
    ![App Screenshot](Assets/modsenable.jpg)
    <br>

    - ServerName: Edit /etc/apache2/apache2.conf dan tambahkan baris berikut untuk mendefinisikan nama server:
    ![App Screenshot](Assets/apache2conf.jpg)
    <br>
    Ini mencegah munculnya peringatan “Could not reliably determine the server's fully qualified domain name”.
    <br>

    - ServerAdmin: Edit /etc/apache2/sites-enabled/000-default.conf dan ubah baris email admin:
    ![App Screenshot](Assets/sitesenable.jpg)
    <br>

    - Pengujian dapat dilakukan dengan mengakses domain melalui browser.
    ![App Screenshot](Assets/pengujian.jpg)
    <br>

3. Custom tampilan halaman
![App Screenshot](Assets/custom.jpg)
<br>

4. Percobaan Akses
![App Screenshot](Assets/successdone.jpg)
<br>
Dari hasil diatas, berarti konfigurasiu web server sudah berhasil.

