# Jarkom-Modul-2-C05-2021
Pengerjaan Soal Shift 2 Jaringan Komputer 2021/2022 ( Modul GNS3, DNS, Web Server )
Cahyadesthian Rizki Widigda (05111940000156)

Ricky Supriyanto (05111940000036)

Ghifari Astaudi U (05111940000012)

# Praktikum Modul 2
## 📅 24-26 September 2021

## 2 Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku
**Jawab:**
1. Pada EinesLobby jalankan command ```vi /root/.bashrc``` dan ketikkan command :
    ```vi /root/bashrc
    echo nameserver 192.168.122.1 > /etc/resolv.conf
    apt-get update
    apt-get install bind9 -y
    apt-get install sudo
    apt-get install nano ```
    Lalu jalankan command berikut ```bash /root/.bashrc``` untuk menginstall command yang sudah diinputkan pada .bashrc
