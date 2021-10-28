# Jarkom-Modul-2-C05-2021
Pengerjaan Soal Shift 2 Jaringan Komputer 2021/2022 ( Modul GNS3, DNS, Web Server )         
.                                                           
Ghifari Astaudi U. (05111940000012)                             
Ricky Supriyanto (05111940000036)                                       
Cahyadesthian R. Widigda (05111940000156)                                   
.                                                               
# Praktikum Modul 2
## 📅 24-26 September 2021
Luffy adalah seorang yang akan jadi Raja Bajak Laut. Demi membuat Luffy menjadi Raja Bajak Laut, Nami ingin membuat sebuah peta, bantu Nami untuk membuat peta berikut:
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/petaluffy.png?raw=trueg" width="800">  
EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet (1)
## 2 Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku
**Jawab:**
1. Pada EinesLobby jalankan command ```vi /root/.bashrc``` dan ketikkan command :
    ```
    echo nameserver 192.168.122.1 > /etc/resolv.conf
    apt-get update
    apt-get install bind9 -y
    apt-get install sudo
    apt-get install nano
      ```
   Lalu jalankan command berikut ```bash /root/.bashrc``` untuk menginstall command yang sudah diinputkan pada .bashrc
2. Buat script.sh dengan command ```bash /root/scipt.sh``` dan buat domain franky.C05.com
.               
.                       
.                       
.                               
## Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie(3).
Pada EniesLobby ```vi /etc/bind/kaizoku/franky.C05.com``` kemudian uncomment bagian
``` 
;ns1\tIN\tA\t192.186.2.4
;super\tIN\tNS\tns1'
```
lalu comment pada bagian 
```
super\tIN\tA\t192.186.2.4\t;soal IP Skypie
```
sehingga ```/etc/bind/kaizoku/franky.C05.com``` seperti berikut                 
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer3-1.png" width="700">                  
kemudian untuk konfigurasi selanjutnya mengenai named.conf.local melalui
```
zone "super.franky.C05.com" {\n\ttype master;\n\tfile "/etc/bind/kaizoku/super.franky.C05.com";\n};
' > /etc/bind/named.conf.local
```
```
echo -e’;
$TTL 	604800
@      	IN      	SOA  	super.franky.C05.com. root.super.franky.C05.com. (
                            	2021102601 	; Serial
                            	604800          	; Refresh
                            	86400             	;Retry
                            	2419200        	; Expire
                            	604800 )        	; Negative Cache TTL
;
@      	IN      	NS           	super.franky.C05.com.
@      	IN      	A            	192.186.2.4  	; IP Skypie
www\tIN\tCNAME\tsuper.franky.C05.com.
' > /etc/bind/kaizoku/super.franky.C05.com
```
kemudian melakukan restart bind9
```
service bind9 restart
```
setelah itu cek melalui client dengan cara melakukan ping terhadap host
```host -t CNAME super.franky.C05.com```dan ```ping www.super.franky.C05.com```                                                     
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer3-2.png" width="700">                                                               
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer3-3.png" width="700">    
                                                                        
## Buat juga reverse domain untuk domain utama (4).                                                             
```zone "2.186.192.in-addr.arpa" {\n\ttype master;\n\tfile "/etc/bind/kaizoku/2.186.192.in-addr.arpa";\n}; >/etc/bind/named.conf.local```                                   
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer4-1.png" width="700">  
```echo -e’$TTL 	604800
@      	IN      	SOA  	franky.C05.com. root.franky.C05.com. (
2021102601 	; Serial
604800          	; Refresh
86400             	;Retry
2419200        	; Expire
604800 )        	; Negative Cache TTL
;
2.186.192.in-addr.arpa.\tIN\tNS\tfranky.C05.com.\n
2\tIN\tPTR\tfranky.C05.com.’ >/etc/bind/kaizoku/2.186.192.in-addr.arpa
```
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer4-2.png" width="700">  
kemudian restart bind9 ```service bind9 restart``` lali melakukan cek pada client 
```host -t PTR 192.186.2.2```
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer4-3.png" width="700">  
                                                                
## Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama (5).
Pada EniesLobby menuliskan IP Water7
```
echo -e’;
zone "franky.C05.com" {\n\t
            type master;\n\t
            notify yes;\n\t
            also-notify { 192.186.2.3; };\n\t
            allow-transfer { 192.186.2.3; };\n\t
            file "/etc/bind/kaizoku/franky.C05.com";\n\t
   }; > /etc/bind/named.conf.local
```
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer5-1.png" width="700">  
kemudia pada Water7 di (/root/.bashrc)
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```
lalu di water7 (/root/script.sh)
```
echo -e’
zone "franky.C05.com" {\n\t
        type slave;\n\t
        masters { 192.186.2.2; };\n\t
        file "/var/lib/bind/franky.C05.com";\n\t
    };’ > /etc/bind/named.conf.local
```                                         
kemudian restart bind9                      
```service bind9 restart```                                                     
dan pada server EniesLobby mematikan service bind9                                                                                                   
```service bind9 stop```                                                    
setelah itu untuk mencoba pada Loguetown ditambahkan ip water 7                                                                                           
```
echo -e 'nameserver 192.186.2.2
nameserver 192.186.2.4
nameserver 192.186.2.3' > /etc/resolv.conf
```
Lalu ping franky.C05.com
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer5-2.png" width="500">  



.                                                   
