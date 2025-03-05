<div align="center">
    <h1 style="text-align: center;font-weight: bold">Tugas Unix-and-Linux-sysadmin-notes<br><br>
    Workshop Administrasi Jaringan</h1>
    <h4 style="text-align: center;">Dosen Pengampu : Dr. Ferry Astika Saputra, S.T., M.Sc.</h4>
</div>
<br />
<div align="center">
    <img src="Assets/Logo_PENS.png" alt="Logo PENS">
    <h3 style="text-align: center;">Disusun Oleh : </h3>
    <p style="text-align: center;">
        <strong>Roihanah Inayati Bashiroh</strong><br>
        <strong>3123500005 / 2 D3 IT A</strong><br>
    </p>

<h3>Politeknik Elektronika Negeri Surabaya<br>Departemen Teknik
Informatika Dan Komputer<br>Program Studi Teknik Informatika<br>2024/2025</h3>
    <hr>
    <hr>
</div>

# Chapter 4: Process Control


## Daftar Isi

- [Components of a Process](#components-of-a-process)
- [The PID: process ID number](#the-pid-process-id-number)
- [The PPID: parent process ID number](#the-ppid-parent-process-id-number)
- [The UID and EUID: user ID and effective user ID](#the-uid-and-euid-user-id-and-effective-user-id)
- [Lifecycle of a Process](#lifecycle-of-a-process)
- [Signals](#signals)
- [Kill: send signals](#kill-send-signals)
- [PS: Monitoring Processes](#ps-monitoring-processes)
- [Interactive monitoring with top](#interactive-monitoring-with-top)
- [Nice and renice: changing process priority](#nice-and-renice-changing-process-priority)
- [The /proc filesystem](#the-proc-filesystem)
- [Strace and truss](#strace-and-truss)
- [Runaway processes](#runaway-processes)
- [Periodic processes](#periodic-processes)
- [cron: schedule command](#cron-schedule-command)
- [Format of crontab](#format-of-crontab)
- [Systemd timer](#systemd-timer)
- [Common use for scheduled tasks](#common-use-for-scheduled-tasks)
- [Kesimpulan](#kesimpulan)
<br>
<br>
### Components of a Process
&nbsp;&nbsp; Proses terdiri dari ruang alamat (address space) dan struktur data dalam kernel. Ruang alamat adalah sekumpulan halaman memori yang digunakan untuk menyimpan kode, data, dan stack proses. Halaman ini biasanya berukuran 4KiB atau 8KiB.

&nbsp;&nbsp; Kernel menggunakan struktur data untuk melacak status proses, prioritas, parameter penjadwalan, dan sumber daya yang digunakan seperti CPU, memori, file terbuka, dan port jaringan.

Informasi penting yang dicatat kernel tentang proses meliputi:

- Peta ruang alamat proses
- Status proses (berjalan, tidur, dll.)
- Prioritas proses
- Sumber daya yang digunakan
- File dan port jaringan yang dibuka
- Sinyal yang diblokir
- Pemilik proses (User ID)

&nbsp;&nbsp; **Thread** adalah konteks eksekusi dalam proses yang berbagi ruang alamat dan sumber daya. Proses dapat memiliki banyak thread untuk melakukan eksekusi secara paralel. Thread lebih ringan dan lebih murah dibuat dibandingkan proses.

&nbsp;&nbsp; Contohnya, pada server web, proses utama mendengarkan koneksi masuk dan membuat thread baru untuk menangani setiap permintaan. Dengan banyak thread, server bisa menangani banyak permintaan secara bersamaan.
<br>
<br>
<br>
### The PID: process ID number
&nbsp;&nbsp; Setiap proses memiliki nomor unik yang disebut **PID (Process ID)**, yang diberikan oleh kernel saat proses dibuat. PID digunakan untuk mengidentifikasi proses dalam berbagai sistem seperti mengirim sinyal.  

&nbsp;&nbsp; Dengan konsep **namespace**, beberapa proses bisa memiliki PID yang sama dalam lingkungan terisolasi seperti **container**, yang memungkinkan menjalankan beberapa instance aplikasi secara terpisah pada satu sistem.
<br>
<br>
<br>
### The PPID: parent process ID number
&nbsp;&nbsp; PPID (Parent Process ID) adalah nomor ID proses induk yang membuat proses tersebut. PPID digunakan untuk merujuk ke proses induk dalam berbagai panggilan sistem, seperti mengirim sinyal ke proses induk.
<br>
<br>
<br>
### The UID and EUID: user ID and effective user ID
&nbsp;&nbsp; **UID** (User ID) adalah ID pengguna yang memulai proses. **EUID** (Effective User ID) adalah ID pengguna yang digunakan proses untuk menentukan akses terhadap sumber daya seperti file, port jaringan, dan lainnya. EUID berperan dalam mengontrol izin akses pada sistem.
<br>
<br>
<br>
### Lifecycle of a Process
&nbsp;&nbsp; Dalam sistem operasi, proses baru dibuat dengan menggunakan sistem pemanggilan **fork**. **fork** membuat salinan proses induk yang hampir identik, tetapi memiliki **PID** (Process ID) yang berbeda dan informasi akuntansi sendiri. Pada Linux, pemanggilan ini secara internal menggunakan **clone**, yang mendukung fitur tambahan seperti thread, namun **fork** tetap dipertahankan untuk kompatibilitas lama.  

&nbsp;&nbsp; Saat sistem menyala (booting), kernel secara otomatis membuat beberapa proses, termasuk **init** atau **systemd** yang memiliki nomor proses 1. Proses ini menjalankan skrip startup sistem. Semua proses lainnya adalah turunan dari proses ini, kecuali proses yang langsung dibuat oleh kernel.
<br>
<br>
<br>
### Signals
&nbsp;&nbsp; Sinyal adalah cara untuk mengirim notifikasi ke suatu proses, digunakan untuk memberi tahu bahwa suatu peristiwa telah terjadi. Terdapat sekitar 30 jenis sinyal yang digunakan untuk berbagai tujuan, seperti:  

- Komunikasi antar proses.  
- Mematikan, menghentikan, atau menunda proses saat tombol tertentu ditekan di terminal.  
- Digunakan administrator untuk tujuan tertentu dengan perintah **kill**.  
- Dikirim oleh kernel saat proses melakukan pelanggaran, seperti pembagian dengan nol.  
- Memberi tahu proses tentang kondisi penting, seperti kematian proses anak atau ketersediaan data pada saluran I/O.
<br>

![App Screenshot](Assets/no1.png)<br>
- **KILL**: Sinyal yang tidak bisa diblokir, langsung menghentikan proses di level kernel tanpa bisa ditangani oleh proses.
- **INT**: Dikirim saat pengguna menekan `Ctrl + C` di terminal, meminta proses berhenti. Program sederhana akan langsung berhenti, sedangkan program interaktif akan membersihkan proses sebelum menunggu input pengguna lagi.
- **TERM**: Permintaan penghentian proses secara penuh, diharapkan proses akan membersihkan status sebelum keluar.
- **HUP**: Dikirim saat terminal ditutup, awalnya untuk menunjukkan sambungan telepon terputus. Sekarang sering digunakan untuk me-restart daemon agar memuat konfigurasi baru.
- **QUIT**: Mirip dengan TERM, tetapi menghasilkan *core dump* jika tidak ditangani. Beberapa program menggunakan sinyal ini untuk fungsi lain.
<br>
<br>
<br>
### Kill: send signals
&nbsp;&nbsp; Perintah **kill** digunakan untuk mengirim sinyal ke proses, biasanya untuk menghentikan proses. Secara default, **kill** mengirim sinyal **TERM** (terminate). Pengguna biasa hanya bisa menghentikan proses miliknya sendiri, sedangkan **root** bisa menghentikan proses apapun.

**Sintaks:**
```bash
kill [-signal] pid
```
- **signal**: Nomor atau nama sinyal yang dikirim.
- **pid**: ID proses yang ingin dihentikan.

&nbsp;&nbsp; Jika sinyal tidak ditentukan, **kill** akan menggunakan sinyal TERM, namun sinyal ini bisa diabaikan oleh proses. Untuk memastikan proses benar-benar mati, gunakan **kill -9 pid** karena sinyal **KILL** tidak bisa diabaikan.

**Perintah Lain:**
- **killall**: Menghentikan proses berdasarkan nama, bukan PID. Contoh:
  
  ```bash
  killall firefox
  ```

- **pkill**: Mirip dengan **killall** tapi memiliki lebih banyak opsi. Contoh:
  
  ```bash
  pkill -u abdoufermat
  ```
  (Menghentikan semua proses milik pengguna **abdoufermat**).