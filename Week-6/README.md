<div align="center">
    <h1 style="text-align: center;font-weight: bold">Konfigurasi pada VM1 dan VM2<br>Workshop Administrasi Jaringan</h1>
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

- [Konfigurasi pada VM 1 (Server)](#konfigurasi-vm-1)  
- [Konfigurasi NTPSec](#konfigurasi-ntpsec-vm-1)  
- [Konfigurasi File Samba](#konfigurasi-samba-vm-1)  
- [Konfigurasi DNS Server](#konfigurasi-dns-server-vm-1)  
- [Konfigurasi pada VM 2 (Client)](#konfigurasi-vm-2)  
- [Kesimpulan](#kesimpulan)  

Dalam praktikum ini, digunakan dua buah Virtual Machine (VM) yang masing-masing berperan sebagai **Client** dan **Server**.

### Konfigurasi VM 1 (Server)

VM 1 bertindak sebagai server. Konfigurasi jaringannya menggunakan dua network adapter, yaitu:

- **Bridge Adapter** yang digunakan untuk akses internet.  
- **Internal Network** yang digunakan sebagai media komunikasi langsung dengan VM Client.

VM 1 juga difungsikan sebagai **gateway** agar client dapat terkoneksi ke internet. Selain itu, VM ini dilengkapi dengan layanan seperti **Samba** untuk berbagi file, **NTPSec** untuk sinkronisasi waktu, dan **Bind9** sebagai DNS lokal.

#### Konfigurasi Network Adapter

![App Screenshot](Assets/no1.jpg)<br>  
![App Screenshot](Assets/no2.jpg)<br> 

Server dikonfigurasi menggunakan dua adapter: **Bridged Adapter** untuk mengakses internet melalui host, dan **Internal Network** untuk komunikasi dengan VM 2. Adapter ini memastikan server dapat berfungsi sebagai penghubung antara client dan internet.

#### Konfigurasi IP Address untuk Koneksi Internal antar VM

1. Mengecek IP Address  
   ![App Screenshot](Assets/no3.jpg)<br> 

2. Mengakses terminal VM 1 via SSH  
   ![App Screenshot](Assets/no4.jpg)<br> 

   Untuk melakukan konfigurasi dari host ke server, koneksi dilakukan secara remote menggunakan protokol SSH (Secure Shell).

3. Menambahkan konfigurasi pada file `/etc/network/interfaces`  
   ![App Screenshot](Assets/no5.jpg)<br> 

   Setiap adapter akan memiliki nama unik. Dalam konfigurasi ini, `enp0s3` digunakan untuk koneksi DHCP (internet), sedangkan `enp0s8` dikonfigurasi secara statis dan difungsikan sebagai gateway ke client.

#### Mengaktifkan IP Forwarding pada Server

1. Edit file `/etc/sysctl.conf`  
   ![App Screenshot](Assets/no6.jpg)<br> 

   Untuk membuat server bisa meneruskan paket jaringan (routing), pengaturan `net.ipv4.ip_forward` harus diaktifkan.

2. Melakukan validasi konfigurasi  
   ![App Screenshot](Assets/no7.jpg)<br> 

#### Konfigurasi iptables

1. Instalasi paket iptables dan iptables-persistent  
   ![App Screenshot](Assets/no8.jpg)<br> 

2. Mengatur aturan iptables pada file `/etc/iptables/rules.v4`  
   ![App Screenshot](Assets/no9.jpg)<br> 

3. Menyimpan konfigurasi dengan perintah `iptables-restore`  
   ![App Screenshot](Assets/no10.jpg)<br> 

#### Reboot

Setelah semua konfigurasi selesai, jalankan perintah `reboot` untuk memuat ulang sistem.

---

### Konfigurasi NTP (NTPSec) pada VM 1

1. Instalasi paket ntpsec  
   ![App Screenshot](Assets/no11.jpg)<br> 

2. Mengatur konfigurasi server NTP  
   ![App Screenshot](Assets/no12.jpg)<br> 

3. Restart layanan ntpsec dengan perintah `systemctl restart ntpsec`  
   ![App Screenshot](Assets/no13.jpg)<br> 

4. Mengecek status layanan NTP  
   ![App Screenshot](Assets/no14.jpg)<br> 

5. Melakukan validasi koneksi NTP  
   ![App Screenshot](Assets/no15.jpg)<br> 

---

### Konfigurasi File Samba pada VM 1

1. Instalasi paket samba  
   ![App Screenshot](Assets/no16.jpg)<br> 

2. Membuat folder share dan mengatur permission  
   ![App Screenshot](Assets/no17.jpg)<br> 

3. Menambahkan konfigurasi pada file `/etc/samba/smb.conf`  
   ![App Screenshot](Assets/no18.jpg)<br>   
   ![App Screenshot](Assets/no32.jpg)<br> 

4. Me-restart layanan samba  
   ![App Screenshot](Assets/no19.jpg)<br> 

---

### Konfigurasi DNS Server pada VM 1 

1. Instalasi paket DNS server  
   ![App Screenshot](Assets/no20.jpg)<br> 

2. Menambahkan konfigurasi pada file `/etc/bind/named.conf`  
   ![App Screenshot](Assets/no21.jpg)<br> 

3. Mengedit file opsi `/etc/bind/named.conf.options`  
   ![App Screenshot](Assets/no22.jpg)<br> 

4. Membuat internal zone di `/etc/bind/named.conf.internal-zones`  
   ![App Screenshot](Assets/no23.jpg)<br> 

5. Menambahkan opsi `-4` pada file `/etc/default/named`  
   ![App Screenshot](Assets/no24.jpg)<br> 

6. Membuat file konfigurasi domain lokal  
   ![App Screenshot](Assets/no25.jpg)<br> 

7. Membuat file konfigurasi berdasarkan IP address  
   ![App Screenshot](Assets/no26.jpg)<br> 

---

### Konfigurasi pada VM 2

VM 2 diatur agar memiliki alamat IP dalam satu subnet dengan interface internal dari VM 1 (enp0s8), sehingga koneksi antar VM dapat berjalan dengan lancar.

![App Screenshot](Assets/no27.jpg)<br> 

#### Melakukan ping ke gateway server

![App Screenshot](Assets/no28.jpg)<br> 

#### Menguji akses folder Samba dari Client

![App Screenshot](Assets/no29.jpg)<br> 

Jika folder **Share** muncul di file manager client, maka konfigurasi file sharing dengan Samba telah berhasil dilakukan.

#### Mengecek DNS Server (Client) menggunakan domain name

![App Screenshot](Assets/no30.jpg)<br>

Hasil dari perintah `dig` menunjukkan bahwa domain `ns.kelompok5.home` berhasil dikenali dan diterjemahkan ke alamat IP yang sesuai, tanpa error.

#### Mengecek DNS Server (Client) menggunakan IP address (Reverse DNS)

![App Screenshot](Assets/no31.jpg)<br>

Perintah `dig -x 192.168.3.1` menunjukkan hasil yang sesuai dengan domain `ns.kelompok5.home`, yang berarti PTR record sudah dikonfigurasi dengan benar.

---

## Kesimpulan

Melalui praktikum ini, saya telah berhasil melakukan serangkaian konfigurasi layanan pada VM1 yang berperan sebagai server serta VM2 sebagai client. Pada **VM1**, konfigurasi jaringan dilakukan dengan dua adapter untuk memastikan konektivitas ke internet dan jaringan internal. Server diatur agar dapat menjadi gateway bagi client melalui pengaktifan IP forwarding dan pengaturan iptables.

Pemasangan dan pengaturan **NTPSec** memungkinkan sinkronisasi waktu agar semua sistem dalam jaringan memiliki waktu yang konsisten dan akurat, yang penting untuk keamanan serta manajemen sistem. **Layanan Samba** berhasil disiapkan untuk memfasilitasi berbagi file antara server dan client secara mudah melalui jaringan lokal. Hal ini dibuktikan dengan client yang dapat mengakses folder shared dari file manager-nya.

Selanjutnya, konfigurasi **DNS Server menggunakan Bind9** juga sukses dilakukan. Server mampu menerjemahkan domain lokal menjadi alamat IP dan sebaliknya, yang ditunjukkan dari hasil tes DNS forward dan reverse lookup tanpa error. Pada sisi **VM2 sebagai client**, koneksi ke server berjalan lancar berkat pengaturan IP statis yang sesuai jaringan, serta berhasil mengakses layanan-layanan yang telah disediakan oleh server.

---

