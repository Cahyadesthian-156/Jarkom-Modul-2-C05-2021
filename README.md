# Jarkom-Modul-2-C05-2021
Pengerjaan Soal Shift 2 Jaringan Komputer 2021/2022 ( Modul GNS3, DNS, Web Server )         
.                                                           
Ghifari Astaudi U. (05111940000012)                             
Ricky Supriyanto (05111940000036)                                       
Cahyadesthian R. Widigda (05111940000156)                                   
.                                                               
# Praktikum Modul 2
### 📅 24-26 September 2021            
.                     
## (1) Luffy adalah seorang yang akan jadi Raja Bajak Laut. Demi membuat Luffy menjadi Raja Bajak Laut, Nami ingin membuat sebuah peta, bantu Nami untuk membuat peta berikut:
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/petaluffy.png?raw=trueg" width="800">  

## EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet


### Jawab        
Langkah pertama yang dilakukan adalah membuat peta seperti pada digambar, kemudian melakukan pengaturan IP untuk setiap nodenya terlebih dahulu, dalam hal ini, kelompok 
C05 memiliki prefix 192.186 sehingga pengaturan nodenya                                       

**Foosha**                            
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
		address 192.186.1.1
		netmask 255.255.255.0

auto eth2
iface eth2 inet static
		address 192.186.2.1
		netmask 255.255.255.0
```

**Loguetown**
```
auto eth0
iface eth0 inet static
		address 192.186.1.2
		netmask 255.255.255.0
		gateway 192.186.1.1
```

**Alabasta**
```
auto eth0
iface eth0 inet static
		address 192.186.1.3
		netmask 255.255.255.0
		gateway 192.186.1.1
```

**EniesLobby**
```
auto eth0
iface eth0 inet static
	address 192.186.2.2
	netmask 255.255.255.0
	gateway 192.186.2.1
```

**Water7**
```
auto eth0
iface eth0 inet static
	address 192.186.2.3
	netmask 255.255.255.0
	gateway 192.186.2.1
```

**Skypie**
```
auto eth0
iface eth0 inet static
	address 192.186.2.4
	netmask 255.255.255.0
	gateway 192.186.2.1
```


kemudian pada Foosha
```
vi /root/.bashrc 
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.186.0.0/16
```
saat melakukan telnet akan langsung melakukan perintah yang berada di .bashrc 
, kemudian di setiap node agar dapat terhubung ke internet, dapat dilakukan dengan mengeksekusi perintah berikut pada Foosha
```
cat /etc/resolv.conf
```
untuk mendapatkan IP(dalam kasus ini 192.168.122. ) yang kemudian diatur pada setiap node dengan perintah berikut
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```
kemudian pada setiap node bisa dilakukan ping google.com atau apt-get update atau lainnya
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer1/alabsta-setup.png" width="400">    
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer1/enieslobby0setup.png" width="400">   
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer1/loguetown-setup.png" width="400">   
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer1/skypie-wetup.png" width="400">   
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer1/water7-setup.png" width="400">   
.                                                                                           
.                                                                                                         
.                                                                                                                                                                                                     
## (2) Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku 


### Jawab    

Pada EniesLobby, Install paket yang dibutuhkan dalam proses
```
apt-get update
apt-get install bind9 -y
apt-get install sudo
apt-get install nano
```
kemudian 
```
nano /etc/bind/named.conf.local
```
dan menambahkan zone seperti berikut
```
zone "franky.C05.com" {
type master;
file "/etc/bind/kaizoku/franky.C05.com";
};
```
Setelah itu membuat folder franky ```mkdir /etc/bind/kaizoku```

Copykan file db.local pada path /etc/bind kedalam folder kaizoku yang sudah dibuat dan ubah namanya menjadi franky.C05.com
```
cp /etc/bind/db.local /etc/bind/kaizoku/franky.C05.com
```
kemudian, masuk ke franky.C05.com ```nano /etc/bind/kaizoku/franky.C05.com```

Kemudian edit file tersebut sebagai beriku dan tambahkan alias dengan record CNAME pada file tersebut:
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer2/nomer2-2.png" width="700"> 

Setelah itu keluar dari file dan melakukan restart bind9 ```service bind9 restart```

Langkah tersebut dimasukkan dalam script.sh dan command.sh seperti berikut:

script.sh
```
echo 'zone "franky.C05.com" {
    	type master;
    	file "/etc/bind/kaizoku/franky.C05.com";
};' > /etc/bind/named.conf.local
echo -e ';
; BIND data file for local loopback interface
;
$TTL	604800
@\tIN\tSOA\tfranky.C05.com. root.franky.C05.com. (
                    	2021        	; Serial
                    	604800      	; Refresh
                    	86400       	; Retry
                    	2419200     	; Expire
                    	604800 )    	; Negative Cache TTL
;
@      	IN      	NS     	franky.C05.com.
@      	IN      	A        	192.186.2.2  	; IP EniestLobby
www	IN      	CNAME          	franky.C05.com.
 ‘> /etc/bind/kaizoku/franky.C05.com
```			


command.sh
```
mkdir /etc/bind/kaizoku
```

kemudian pada Node Client ( Loguetown dan Alabasta )
```
nano /etc/resolv.conf
 ```
Atur nameserver seperti berikut:
```
nameserver 192.186.2.2 ;IP EniesLobby
```
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer2/nomer2-3.png" width="700"> 


Masukkan perintah pada script.sh
```
echo 'nameserver 192.186.2.2
nameserver 192.186.2.4
nameserver 192.186.2.3' > /etc/resolv.conf
```

lalu melakukan percobaan ping franky.C05.com dan host -t CNAME www.franky.C05.com
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer2/nomer2-4.png" width="700">

.                                         
.                                                                                                                                 
.                                         

## (3)Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie

### Jawab
Pada EniesLobby, buka file franky.C05.com dan edit file tersebut dan tambahkan konfigurasi subdomain untuk franky.yyy.com yaitu super.franky.yyy.com                      
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/praktikum/praktikumjarkom2/nomer3/nomer3-1.jpg" width="700">
```
$TTL 	604800
@      	IN      	SOA  	franky.C05.com. root.franky.C05.com. (
                    2021102601 	          ; Serial
                        604800          	; Refresh
                         86400           	;Retry
                       2419200         	  ; Expire
                        604800 )        	; Negative Cache TTL
;
@      		  IN      	NS           	franky.C05.com.
@      		  IN      	A            	192.186.2.2  	; IP EniestLobby
www    	    IN      	CNAME        	franky.C05.com.
super  	    IN      	A            	192.186.2.4   ;IPSkypie
@      		  IN      	A            	192.186.2.4
```


kemudian buat Zone baru ```nano /etc/bind/named.conf.local```                                               
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer3/nomer3-2.jpg" width="700">
```
zone "super.franky.C05.com" {
        	type master;
        	file "/etc/bind/kaizoku/super.franky.C05.com";
};
```

Copy db.local ke folder ./kaizoku
```
cp /etc/bind/db.local /etc/bind/kaizoku/super.franky.C05.com
```
lakukan konfigurasi pada super.franky.C05.com                                         
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer3/nomer3-3.jpg" width="700">

```
$TTL 	604800
@      	IN      	SOA  	franky.C05.com. root.franky.C05.com. (
                            	2021102601 	      ; Serial
                            	604800          	; Refresh
                            	86400             ;Retry
                            	2419200        	  ; Expire
                            	604800 )        	; Negative Cache TTL
;
@      		IN      	NS           	super.franky.C05.com.
@      		IN      	A            	192.186.2.4  	; IP Skypie
www     	IN      	CNAME        	super.franky.C05.com.
```

Perintah tersebut dimasukkan dalam script.sh
```
echo -e ';
; BIND data file for local loopback interface
;
$TTL	604800
@\tIN\tSOA\tfranky.C05.com. root.franky.C05.com. (
                    	2021        	; Serial
                    	604800      	; Refresh
                    	86400       	; Retry
                    	2419200     	; Expire
                    	604800 )    	; Negative Cache TTL
;
@           	IN  	NS          	franky.C05.com.
@           	IN  	A           	192.186.2.2         	;IP EinesLobby
www         	IN  	CNAME       	franky.C05.com.     	;CNAME Buat Alias
ns1         	IN  	A           	192.186.2.4         	;IP Skypie
super       	IN  	NS          	ns1
ns2         	IN  	A           	192.186.2.3         	; IP Water7
mecha       	IN  	NS          	ns2' > /etc/bind/kaizoku/franky.C05.com
```

```
echo 'zone "franky.C05.com" {
    	type master;
    	notify yes;
    	also-notify { 192.186.2.3; };
    	allow-transfer { 192.186.2.3; };
    	file "/etc/bind/kaizoku/franky.C05.com";
};
zone "super.franky.C05.com" {
    	type master;
    	file "/etc/bind/kaizoku/super.franky.C05.com";

};’>  /etc/bind/named.conf.local
```

```
echo -e ';
$TTL	604800
@   	IN          	SOA 	super.franky.C05.com. root.super.franky.C05.com. (
                            	2021102601  	; Serial
                            	604800      	; Refresh
                            	86400       	;Retry
                            	2419200     	; Expire
                            	604800 )    	; Negative Cache TTL
;
@   	IN          	NS          	super.franky.C05.com.
@   	IN          	A           	192.186.2.4 	; IP Skypie
www 	IN          	CNAME       	super.franky.C05.com.
' > /etc/bind/kaizoku/super.franky.C05.com
```

Kemudian restart bind9 dengan perintah ```service bind9 restart```


Pada node client (Loguetown dan Alabasta), arahkan nameserver menuju IP Skypie dengan mengedit file resolv.conf dengan mengetikkan perintah ```nano /etc/resolv.conf``` lalu tambahkan

```
nameserver 192.186.2.2
nameserver 192.186.2.4
```                                                                                           
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer3/nomer3-4.jpg" width="700">       

kemudian lakukan ping pada super.franky.C05.com dan www.super.franky.C05.com atau host -t CNAME www.super.franky.C05.com dan www.super.franky.C05.com                     
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/praktikum/praktikumjarkom2/nomer3/nomer3-5.jpg" width="700">                         
.                                                                                         
.                                                                            
.                             
## (4) Buat juga reverse domain untuk domain utama 


### Jawab
Pada EniesLobby, edit file /etc/bind/named.conf.local ```nano /etc/bind/named.conf.local```
tambahkan
```
zone "2.186.192.in-addr.arpa" {
type master;
file "/etc/bind/kaizoku/2.186.192.in-addr.arpa";
};
```
sehingga zone menjadi                                                 
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer4/nomer4-1.jpg" width="700">


kemudian copy file db.local pada path /etc/bind ke dalam folder kaizoku yang baru saja dibuat dan ubah namanya menjadi 2.186.192.in-addr.arpa

```
cp /etc/bind/db.local /etc/bind/kaizoku/2.186.192.in-addr.arpa
```


lalu edit file 2.186.192.in-addr.arpa                                                               
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer4/nomer4-2.jpg" width="700"> 


Perintah tersebut dimasukkan dalam script.sh

```
echo 'zone "franky.C05.com" {
        type master;
        notify yes;
        also-notify { 192.186.2.3; };
        allow-transfer { 192.186.2.3; };
        file "/etc/bind/kaizoku/franky.C05.com";
};
zone "super.franky.C05.com" {
        type master;
        file "/etc/bind/kaizoku/super.franky.C05.com";
};
zone "2.186.192.in-addr.arpa" {
        type master;
        file "/etc/bind/kaizoku/2.186.192.in-addr.arpa";
};' > /etc/bind/named.conf.local
```


```
echo -e ';
$TTL	604800
@   	IN  	SOA 	franky.C05.com. root.franky.C05.com. (
                    	2021102601          	; Serial
                    	604800              	; Refresh
                    	86400               	;Retry
                    	2419200             	; Expire
                    	604800 )            	; Negative Cache TTL
;
2.186.192.in-addr.arpa.     	IN  	NS  	franky.C05.com.
2                           	IN  	PTR 	franky.C05.com. '>/etc/bind/kaizoku/2.186.192.in-addr.arpa
```


Kemudian restart bind9 dengan perintah ```service bind9 restart```

setelah itu pada node client (Loguetown dan Alabasta) cek dengan melakukan 
```host -t PTR 192.186.2.2```

<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer4/nomer4-3.jpg" width="700">     

.                                                                                         
.                                                                                                           
.                                                                       
## (5)Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama 

### Jawab

Pada EniesLobby, lakukan ```nano /etc/bind/named.conf.local```
lalu ubah seperti pada bagian berikut

```
zone "franky.C05.com" {
            type master;
            notify yes;
            also-notify { 192.186.2.3; };
            allow-transfer { 192.186.2.3; };
            file "/etc/bind/kaizoku/franky.C05.com";
}; 
```                                                                                                                              
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer5/nomer5-1.jpg" width="700">   



Pada Water7
Masukkan command dan install paket yang dibutuhkan dan perintah tersebut di masukkan dalam /root/.bashrc:

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```

Kemudian berikan pengaturan Slave di ```/etc/bind/named.conf.local```

```
zone "franky.C05.com" {
        type slave;
        masters { 192.186.2.2; };
        file "/var/lib/bind/franky.C05.com";
 }; 
```                                                                       
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer5/nomer5-2.jpg" width="700">   


Perintah tersebut dimasukkan dalam script.sh
```
echo -e '
zone "franky.C05.com" {
    	type slave;
    	masters { 192.186.2.2; };
    	file "/var/lib/bind/franky.C05.com";
};' > /etc/bind/named.conf.local
```

kemudian restart bind9 ```service bind9 restart```

setelah itu pada Loguetown dan Alabasta tambahkan IP Water7 sehingga nampak seperti                                       
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer5/nomer5-3.jpg" width="700">   

Untuk melakukan cek keberhasilan konfigurasi, matikan bind9 yang ada pada EniesLobby dengan command:
``service bind9 stop```

lalu di node client (Loguetown dan Alabasta) lakukan ping franky.C05.com                                            
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer5/nomer5-4.jpg" width="700">   
.                                                   
.                                                                 
.   

## (6) Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo

### Jawab
Pada EniesLobby, edit file /etc/bind/kaizoku/franky.C05.com

```
$TTL 	604800
@      	IN      	SOA  	franky.C05.com. root.franky.C05.com. (
              2021102601 		        ; Serial
                  604800          	; Refresh
                   86400           	;Retry
                 2419200           	; Expire
                  604800 )        	; Negative Cache TTL
;
@     	 	  	IN      	NS     	    franky.C05.com.
@      		  	IN      	A	    192.186.2.2  	; IP EniestLobby
www		      	IN      	CNAME       franky.C05.com.
super		    	IN		A	    192.186.2.4 	;IP Skypie
ns1			IN	      	A	    192.186.2.4
mecha	      		IN	      	NS	    ns1
www.mecha	  	IN	      	CNAME	    192.186.2.4
```															
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer6/nomer6-1.jpg" width="700">   
			


Edit file /etc/bind/named.conf.options dengan comment bagian 
```‘dnssec-validation auto;’``` dan menambahkan 
```
`allow-query{any;};
```											
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer6/nomer6-2.jpg" width="700">   			


kemudian pada /etc/bind/named.conf.local, edit sebagai berikut
<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer6/nomer6-3.jpg" width="700">   



Pada Water7, di /etc/bind/named.conf.local

```
zone "mecha.franky.C05.com" {\n\t
            	type master;\n\t
            	file "/etc/bind/sunnygo/mecha.franky.C05.com";\n\t
}
```

Lalu mkdir /etc/bind/sunnygo

Setelah itu membuat file pada directory /etc/bind/sunnygo/mecha.franky.C05.com dengan isi :

```
$TTL 	604800
@      	IN      	SOA  	mecha.franky.C05.com. root.mecha.franky.C05.com. (
                  2021102601 	          ; Serial
                      604800          	; Refresh
                       86400           	; Retry
                     2419200          	; Expire
                      604800 )        	; Negative Cache TTL
;
@      		IN      	NS     			mecha.franky.C05.com.
@      		IN      	A        		192.186.2.4  	; IP Skypie
general	  	IN	      	A		        192.186.2.4
www		IN	      	CNAME		      	mecha.franky.C05.com.
```

kemudian pada node client ( Loguetown dan Alabasta ) dapat dilakukan ```ping mecha.franky.C05.com```  dan  ```ping www.mecha.franky.C05.com```					

<img src="https://github.com/Cahyadesthian-156/Jarkom-Modul-2-C05-2021/blob/main/screenshot/nomer6/nomer6-7.jpg" width="700">   						
.         												                
.                				         
.               					


## (7)Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie

### Jawab









.                               

## Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.
### Jawab
** Pada Skypie**
Install paket yang diperlukan terlebih dahulu (Masukkan pada /root/.bashrc) dan jalankan (bash /root/.bashrc)
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0 -y
apt-get install nano
apt-get install unzip -y
apt-get install wget -y
```
Masukkan perintah buat directory, download file yang ingin di download di command.sh, pindahkan file yang di download ke directory yang sudah ditentukan, dan lakukan copy pada 000-default.conf ke franky.C05.com.conf
```
mkdir /var/www/franky.C05.com
wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/franky.zip
unzip franky.zip
mv franky/home.html /var/www/franky.C05.com
mv franky/index.php /var/www/franky.C05.com
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/franky.C05.com.conf
```
**Pada EniesLobby**

Lakukan konfigurasi pada zone `franky.C05.com`

Lakukan restart bind9 dengan perintah `Service bind9 restart`

**Pada Loguetown dan Alabasta**
Lakukan instalasi pada lynx dengan mengetikkan perintah `apt-get install lynx -y`
Lalu jalankan perintah `lynx franky.C05.com`

## Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache 
**Pada router Skypie**

Edit file `super.franky.C05.com.conf`pada directory `/etc/apache2/sites-available` dan tambahkan 
``` 
    ErrorDocument 404 /error/404.html
    <Files "/error/404.html">
    <If "-z %{ENV:REDIRECT_STATUS}">
          RedirectMatch 404 /error/404.html$
    </If>
    </Files> 
```
Kemudian restrart dengan perintah ``` service apache2 restart ```
**Pada Loguetown dan Alabasta**

Lakukan perintah ```lynx super.franky.C05.com/rickygantengbosskuh ``` untuk melihat hasil errornya

## Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js
**Pada router Skypie**

Edit file `super.franky.C05.com.conf` pada directory `/etc/apache2/sites-available` dan tambahkan
```Alias "/js" "/var/www/super.franky.C05.com/public/js"```
Lakukan ```a2ensite super.franky.C05.com.conf```
Kemudian restrart dengan perintah ``` service apache2 restart ```

Pada Loguetown dan Alabasta
Lakukan perintah ```lynx super.franky.C05.com/js ``` untuk melihat hasilnya

## Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500
**Pada router Skypie**

Pada command.sh beri perintah berikut, dengan langkah membuat directory terlebih dahulu, setelah itu mendownload file zip dan meng unzip file tersebut.Lalu dilakukan pemindahan data dari super.franky ke super.franky.C05.com. Setelah itu lakukan copy pada 000-default.conf 
```
mkdir /var/www/general.mecha.franky.C05.com
wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/general.mecha.franky.zip
unzip general.mecha.franky.zip

mv general.mecha.franky/drag.wav /var/www/general.mecha.franky.C05.com
mv general.mecha.franky/duck-duck-go.jpg /var/www/general.mecha.franky.C05.com
mv general.mecha.franky/f1.png /var/www/general.mecha.franky.C05.com
mv general.mecha.franky/funko-pop.jpg /var/www/general.mecha.franky.C05.com
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/general.mecha.franky.C05.com.conf
```
Edit file general-script.sh untuk directory /etc/apache2/sites-available/general.mecha.franky.C05.com.conf
```
      echo '<VirtualHost *:15000 *:15500>
    	ServerAdmin webmaster@localhost
    	ServerName general.mecha.franky.C05.com
    	ServerAlias www.general.mecha.franky.C05.com
    	DocumentRoot /var/www/general.mecha.franky.C05.com

    	<Directory /var/www/general.mecha.franky.C05.com>
            	Options +FollowSymLinks -Multiviews
            	AllowOverride All
    	</Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>' > /etc/apache2/sites-available/general.mecha.franky.C05.com.conf
```
Edit file ports.conf dengan menambahakan port `15000` dan `15500` pada directory `/etc/apache2/ports.conf`
echo '
```
# If you just change the port or add more ports here,  will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf
Listen 80
Listen 15000
Listen 15500

<IfModule ssl_module>
    	Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    	Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet' >  /etc/apache2/ports.conf
```
Jalankan perintah a2ensite general.mecha.franky.C05.com.conf
Kemudian restrart dengan perintah ``` service apache2 restart ```

**Pada EniesLobby**

Lakukan konfigurasi pada zone general.mecha.franky.C05.com

Lakukan restart bind9 dengan perintah `Service bind9 restart`

**Pada Loguetown dan Alabasta **

Jalankan perintah`lynx general.mecha.franky.C05.com :15000`

## dengan authentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy
**Pada router Skypie**

Buat username (luffy) dan password dengan menginputkan comment `htpasswd -c /etc/apache2/.htpasswd luffy` Kemudian isi password sesuai kiteria (onepiece)

Buat file `.htaccess` pada `/var/www/general.mecha.franky.C05.com/.htaccess` dan edit file `.htaccess` seperti:

echo 'AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user' > /var/www/general.mecha.franky.C05.com/.htaccess

Edit file general.mecha.franky.C05.com.conf pada directory /etc/apache2/sites-available/general.mecha.franky.C05.com.conf dengan menambahkan:
```
<Directory /var/www/general.mecha.franky.C05.com>
      Options +FollowSymLinks -Multiviews
      AllowOverride All
</Directory>
```
Kemudian restrart dengan perintah ``` service apache2 restart ```

**Pada Loguetown dan Alabasta**
Jalankan perintah `lynx general.mecha.franky.C05.com :15000`
## Dan setiap kali mengakses IP Skypie akan diahlikan secara otomatis ke www.franky.yyy.com
**Pada Skypie**

Buat file `.htacces` pada directory `/var/www/html` dan edit sebagai berikut
```
echo '
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^192\.186\.2\.4$
RewriteRule ^(.*)$ http://www.franky.C05.com [L,R=301] ' > /var/www/html/.htaccess
```

Kemudian, edit file `/000-default.conf`  pada directory `/etc/apache2/sites-available` dengan menambahkan:
```
<Directory /var/www/html>
     Options +FollowSymLinks -Multiviews
     AllowOverride All
</Directory>
```
Kemudian restrart dengan perintah ``` service apache2 restart ```

**Pada EniesLobby**

Lakukan konfigurasi pada zone franky.C05.com

Lakukan restart bind9 dengan perintah `Service bind9 restart`

**Pada Loguetown dan Alabasta**
Jalankan perintah `lynx 192.186.2.4`

## Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png. Maka bantulah Luffy untuk membuat konfigurasi dns dan web server ini!

**Pada Skypie**
Edit file pada `super.franky.C05.com.conf` pada directory `/etc/apache2/sites-available/` dengan menambahkan:
 ```
 <Directory /var/www/super.franky.C05.com>
       Options +FollowSymLinks -Multiviews
       AllowOverride All
</Directory>
```	
Buat file dan `edit /.htaccess` pada directory `/var/www/super.franky.C05.com/` dengan perintah
```
echo 'RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)franky(.*)\.(jpg|gif|png)$ http://super.franky.C05.com/public/images/franky.png [L,R]
' > /var/www/super.franky.C05.com/.htaccess
```                                                   
Jalankan perintah `a2ensite super.franky.C05.com.conf`
Kemudian restrart dengan perintah ` service apache2 restart `

**Pada EniesLobby**

Lakukan konfigurasi pada zone super.franky.C05.com

Lakukan restart bind9 dengan perintah `Service bind9 restart`

**Pada Loguetown dan Alabasta**

Jalankan command `lynx http://super.franky.C05.com/public/images/franky.png`
