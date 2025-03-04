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

# Chapter 5: The Filesystem
![filesystem-icon](https://miro.medium.com/v2/resize:fit:752/1*quw0WvsLLCxad3WC6fjQ1Q.png)



## Daftar Isi

- [Pathnames](#pathnames)
- [Filesystem Mounting and Unmounting](#filesystem-mounting-and-unmounting)
- [Organization of the file tree](#organization-of-the-file-tree)
- [File types](#file-types)
- [File attributes](#file-attributes)
- [Permission bits](#permission-bits)
- [The setuid and setgid bits](#the-setuid-and-setgid-bits)
- [The sticky bit](#the-sticky-bit)
- [ls: list and inspect files](#ls-list-and-inspect-files)
- [chmod: change permissions](#chmod-change-permissions)
- [chown: change ownership](#chown-change-ownership)
- [chgrp: change group](#chgrp-change-group)
- [umask: set default permissions](#umask-set-default-permissions)
- [Access Control Lists](#access-control-lists)
- [Implementation of ACLs](#implementation-of-acls)
- [POSIX ACLs](#posix-acls)
- [NFSv4 ACLs](#nfsv4-acls)
- [Kesimpulan](#kesimpulan)