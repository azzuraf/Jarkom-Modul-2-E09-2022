# Jarkom-Modul-2-E09-2022
### Anggota:
1. Lia Kharisma Putri (5025201034)
2. Azzura Ferliani Ramadhani (5025201190)
3. Ingwer Ludwig Nommensen (5025201259)

## Nomor 1
**WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet.**<br/><br/>
![image](https://user-images.githubusercontent.com/52819640/198832193-9634ea13-a95a-4a26-839d-0c7bb8880319.png)<br/>
Membuat topologi seperti gambar di atas
Mengatur konfigurasi tiap node menjadi seperti berikut:
```sh
- Ostania : 10.26.0.0     | Router
- WISE : 10.26.1.2        | DNS Master
- SSS : 10.26.2.2         | Client
- Garden : 10.26.2.3      | Client
- Berlint : 10.26.3.2     | DNS Slave
- Eden : 10.26.3.3        | Web Master
```
Menyambungkan router ke internet dengan command ``` iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.26.0.0/16```
## Nomor 2
**Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise.**<br/><br/>
Lakukan update dan install bind9 terlebih dahulu
```bash
apt-get update
apt-get install bind9 -y
```
Isikan configurasi domain wise.e09.com sesuai dengan syntax berikut:
```bash
zone "wise.e09.com" {
        type master;
        file "/etc/bind/wise/wise.e09.com";
};
```
Buat folder wise di dalam /etc/bind dengan menggunakan command ```mkdir /etc/bind/wise```
Selanjutnya buat file ```wise.e09.com``` dengan isian sebagai berikut:
```bash
\$TTL    604800
@       IN      SOA     wise.e09.com. root.wise.e09.com. (
                        2               ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@               IN      NS      wise.e09.com.
@               IN      A       10.26.1.2 ; IP Wise
www             IN      CNAME   wise.e09.com.
```
Lakukan restart bind9 dengan command ```service bind9 restart```

## Nomor 3
**Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden.**<br/><br/>
Pada konfigurasi DNS Master Wise, dapat ditambahkan IP address dari Eden dan alamat aliasnya yang akan mengarah ke Eden
```bash
\$TTL    604800
@       IN      SOA     wise.e09.com. root.wise.e09.com. (
                        2               ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
@              IN      NS      wise.e09.com.
@              IN      A       10.26.1.2 ; IP Wise
www            IN      CNAME   wise.e09.com.
eden           IN      A       10.26.3.3 ; IP Eden
www.eden       IN      CNAME   eden.wise.e09.com.
```
Lakukan restart bind9 dengan command ```service bind9 restart```

## Nomor 4
**Buat juga reverse domain untuk domain utama.**<br/><br/>
Tambahkan zone reverse DNS pada file /etc/bind/named.conf.local
```bash
zone "2.26.10.in-addr.arpa" {
        type master;
        file "/etc/bind/wise/2.26.10.in-addr.arpa";
};
```
Tambahkan reverse DNS (NS dan PTR) pada file /etc/bind/wise/2.26.10.in-addr.arpa
```bash
\$TTL    604800
@       IN      SOA     wise.e09.com. root.wise.e09.com. (
                        2               ; Serial
                        604800          ; Refresh
                        86400           ; Retry
                        2419200         ; Expire
                        604800 )        ; Negative Cache TTL
;
2.26.10.in-addr.arpa.   IN      NS      wise.e09.com.
2                       IN      PTR     wise.e09.com.
```
Lakukan restart bind9 dengan command ```service bind9 restart```
## Nomor 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden

Pada server Berlint buat domain untuk strix.operation.wise.e09.com dengan alias www.strix.operation.wise.e09.com. Instalasi bind terlebih dahulu
```bash
apt-get update
apt-get install bind9 -y
```
Edit folder operation.wise.e09.com
```bash
nano /etc/bind/operation/operation.wise.e09.com
```

Restart bind9
```bash
service bind9 restart
```

Lakukan ping ke domain strix.operation.wise.e09.com dan www.strix.operation.wise.e09.com dari client

## Nomor 8

- Untuk konfigurasi web server diperlukan instalasi update, apache, dan php
- Setelah itu konfigurasi server dibuat sesuai berikut dengan ServerName adalah `wise.e09.com` dan ServerAliasnya adalah `www.wise.e09.com` File ini disimpan dalam folder `/etc/apache2/sites-available/wise.e09.com`.
- Kemudian file requirement untuk wise dipindahkan ke `var/www/wise.e09.com`
- Setelah itu konfigurasi web server yang sudah dibuat di-enable dengan command `a2ensite wise.e09.com` dn
```bash
-apt-get install apache2 -y
service apache2 start
apt-get install php -y
apt-get install libapache2-mod-php7.0 -y
service apache2 
apt-get install ca-certificates openssl -y
apt-get install unzip -y
apt-get install git -y
(download menggunakan wget)
unzip -o /root/Praktikum-Modul-2-Jarkom/\*.zip -d /root/Praktikum-Modul-2-Jarkom
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.e09.com
        ServerName wise.e09.com
        ServerAlias www.wise.e09.com

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
" > /etc/apache2/sites-available/wise.e09.com.conf
a2ensite wise.e09.com
mkdir /var/www/wise.e09.com
cp -r /root/Praktikum-Modul-2-Jarkom/wise/. /var/www/wise.e09.com
service apache2 restart
```

## Nomor 9

- Dalam mengubah URL `www.wise.yyy.com/index.php/home` menjadi `www.wise.yyy.com/home`, module RewriteRule digunakan pada file `/var/www/wise.e09.com/.htaccess` untuk dapat mengakses file .php.
- Tambahkan directory `/var/www/wise.e09.com` pada file `/etc/apache2/sites-available/wise.e09.com.conf`
- Restart apache2 dan url .php seharusnya sudah terubah.
```bash
a2enmod rewrite
service apache2 restart
echo "
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php/\$1 [L]
" >/var/www/wise.e09.com/.htaccess
echo "
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/wise.e09.com
        ServerName wise.e09.com
        ServerAlias www.wise.e09.com

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.e09.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/wise.e09.com.conf
service apache2 restart
```

## Nomor 10

- Tambahkan konfigurasi untuk alamat `eden.wise.e09.com` dengan serverAlias `www.eden.wise.e09.com` pada file `/etc/apache2/sites-available/eden.wise.e09.com.conf`
- Gunakan `a2ensite eden.wise.e09.com` untuk mengaktifkan konfigurasi yang terlah dibuat
- Restart apache2 dan konfigurasi web server sudah siap digunakan

```bash
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.e09.com
        ServerName eden.wise.e09.com
        ServerAlias www.eden.wise.e09.com

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.e09.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.e09.com.conf
a2ensite eden.wise.e09.com
mkdir /var/www/eden.wise.e09.com
cp -r /root/Praktikum-Modul-2-Jarkom/eden.wise/. /var/www/eden.wise.e09.com
service apache2 restart
echo "<?php echo 'yes nomor 10' ?>" > /var/www/eden.wise.e09.com/index.php
```

## Nomor 11

- Tambahkan directory `/var/www/eden.wise.e09.com/public` tanpa opsi `AllowOverride All` dan hanya dengan opsi `Options +Indexes` pada file `/etc/apache2/sites-available/eden.wise.e09.com.conf`
- Restart apache2 dan konfigurasi web server sudah siap digunakan

```bash
echo "
<VirtualHost *:80>

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/eden.wise.e09.com
        ServerName eden.wise.e09.com
        ServerAlias www.eden.wise.e09.com

        <Directory /var/www/eden.wise.e09.com/public>
                Options +Indexes
        </Directory>

        ErrorLog \${APACHE_LOG_DIR}/error.log
        CustomLog \${APACHE_LOG_DIR}/access.log combined

        <Directory /var/www/wise.e09.com>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/eden.wise.e09.com.conf
service apache2 restart
```

## Nomor 12

- Tambahkan konfigurasi ErrorDocument ddan diganti dengan halaman `/error/404.html` pada file `/etc/apache2/sites-available/eden.wise.e09.com.conf`
- Restart apache2 dan konfigurasi web server sudah siap digunakan

## Nomor 13

- Tambahkan konfigurasi Alias `/js` untuk mempersingkat url `/var/www/eden.wise.e09.com/public/js` pada file `/etc/apache2/sites-available/eden.wise.e09.com.conf`
- Restart apache 2 dan konfigurasi web server sudah siap digunakan

## Nomor 14
Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500**

- Tambahkan configuration pada /etc/apache2/sites-available/strix.operation.wise.e09.com.conf
- Setting port
- Buat folder /var/www/strix.operation.wise.e09.com dan masukkan resource yang ada ke daam folder tersebut
- Restart apache2 kemudian akses www.strix.operation.wise.yyy.com dari client menggunakan port 15000 atau 15500


## Nomor 15
dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy**

- Masukkan command pada Eden
- Tambahkan configuration pada /etc/apache2/sites-available/strix.operation.wise.e09.com.conf
- Restart apache2 kemudian akses www.strix.operation.wise.yyy.com dari client menggunakan username dan password yang sudah di-setup

## Nomor 16
dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com**

- Tambahkan konfigurasi pada /etc/apache2/sites-available/000-default.conf
- Tambahkan konfigurasi port apache2
- Enable 000-default.conf kemudian restart service apache2, akses IP Eden dari client

## Nomor 17
Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring "eden" akan diarahkan menuju eden.png**

- Tambahkan module rewrite pada .htaccess
- Tambahkan konfigurasi pada /etc/apache2/sites-available/eden.wise.e09.com.conf
- mudian enable module rewrite dan restart service apache2. Akses gambar dari client.
