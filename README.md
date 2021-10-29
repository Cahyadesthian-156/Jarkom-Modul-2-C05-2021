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

kemudian pada Water7 di (/root/.bashrc)
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

kemudian melakukan restart bind9 ```service bind9 restart```                                                     
dan pada server EniesLobby mematikan service bind9 ```service bind9 stop```                                                         
setelah itu untuk mencoba pada Loguetown ditambahkan ip water 7                                                                                           
```
echo -e 'nameserver 192.186.2.2
nameserver 192.186.2.4
nameserver 192.186.2.3' > /etc/resolv.conf
```
Lalu ping franky.C05.com                                                                    
<img src="https://github.com/Cahyadesthian-156/empty/blob/main/nomer5-2.png" width="500">  

## Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache 
Pada router Skypie, Edit file `super.franky.C05.com.conf`pada directory `/etc/apache2/sites-available` dan tambahkan 
``` 
    ErrorDocument 404 /error/404.html
    <Files "/error/404.html">
    <If "-z %{ENV:REDIRECT_STATUS}">
          RedirectMatch 404 /error/404.html$
    </If>
    </Files> 
```
Lakukan ``` a2ensite super.franky.C05.com.conf ```
Kemudian restrart dengan perintah ``` service apache2 restart ```
Pada Loguetown dan Alabasta
Lakukan perintah ```lynx super.franky.C05.com/rickygantengbosskuh ``` untuk melihat hasil errornya

## Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js
Pada router Skypie, Edit file `super.franky.C05.com.conf` pada directory `/etc/apache2/sites-available` dan tambahkan
```Alias "/js" "/var/www/super.franky.C05.com/public/js"```
Lakukan ```a2ensite super.franky.C05.com.conf```
Kemudian restrart dengan perintah ``` service apache2 restart ```

Pada Loguetown dan Alabasta
Lakukan perintah ```lynx super.franky.C05.com/js ``` untuk melihat hasilnya

## Dan Luffy meminta untuk web www.general.mecha.franky.yyy.com hanya bisa diakses dengan port 15000 dan port 15500
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
Pada router Skypie
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
Lakukan a2ensite general.mecha.franky.C05.com.conf
Kemudian restrart dengan perintah ``` service apache2 restart ```

Pada EniesLobby

Lakukan konfigurasi pada zone general.mecha.franky.C05.com

Lakukan restart bind9 dengan perintah `Service bind9 restart`

Pada Loguetown dan Alabasta jalankan perintah
`lynx general.mecha.franky.C05.com :15000`

## dengan authentikasi username luffy dan password onepiece dan file di /var/www/general.mecha.franky.yyy
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

Lakukan a2ensite general.mecha.franky.C05.com.conf
Kemudian restrart dengan perintah ``` service apache2 restart ```

Pada Loguetown dan Alabasta jalankan perintah
`lynx general.mecha.franky.C05.com :15000`
## Dan setiap kali mengakses IP Skypie akan diahlikan secara otomatis ke www.franky.yyy.com
Pada Skypie
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

Pada EniesLobby

Lakukan konfigurasi pada zone franky.C05.com

Lakukan restart bind9 dengan perintah `Service bind9 restart`

Pada Loguetown dan Alabasta
Jalankan perintah `lynx 192.186.2.4`

## Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website www.super.franky.yyy.com, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring “franky” akan diarahkan menuju franky.png. Maka bantulah Luffy untuk membuat konfigurasi dns dan web server ini!
Pada Skypie
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
Kemudian restrart dengan perintah ``` service apache2 restart ```

Pada EniesLobby

Lakukan konfigurasi pada zone super.franky.C05.com

Lakukan restart bind9 dengan perintah `Service bind9 restart`

Pada Loguetown dan Alabasta

Jalankan command `lynx http://super.franky.C05.com/public/images/franky.png`
