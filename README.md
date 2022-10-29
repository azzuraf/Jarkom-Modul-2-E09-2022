# Jarkom-Modul-2-E09-2022

## Soal 1
WISE akan dijadikan sebagai DNS Master, Berlint akan dijadikan DNS Slave, dan Eden akan digunakan sebagai Web Server. Terdapat 2 Client yaitu SSS, dan Garden. Semua node terhubung pada router Ostania, sehingga dapat mengakses internet.<br/><br/>
![image](https://user-images.githubusercontent.com/52819640/198832193-9634ea13-a95a-4a26-839d-0c7bb8880319.png)<br/>
1. Membuat topologi seperti gambar di atas
2. Mengatur konfigurasi tiap node menjadi seperti berikut:
```sh
- Ostania : 10.26.0.0     | Router
- WISE : 10.26.1.2        | DNS Master
- SSS : 10.26.2.2         | Client
- Garden : 10.26.2.3      | Client
- Berlint : 10.26.3.2     | DNS Slave
- Eden : 10.26.3.3        | Web Master
```
3. Menyambungkan router ke internet dengan command ``` iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.26.0.0/16```
## Soal 2

7. Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden

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


**14. Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500**

- Tambahkan configuration pada /etc/apache2/sites-available/strix.operation.wise.e09.com.conf
- Setting port
- Buat folder /var/www/strix.operation.wise.e09.com dan masukkan resource yang ada ke daam folder tersebut
- Restart apache2 kemudian akses www.strix.operation.wise.yyy.com dari client menggunakan port 15000 atau 15500


**15. dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy**

- Masukkan command pada Eden
- Tambahkan configuration pada /etc/apache2/sites-available/strix.operation.wise.e09.com.conf
- Restart apache2 kemudian akses www.strix.operation.wise.yyy.com dari client menggunakan username dan password yang sudah di-setup

**16. dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com**

- Tambahkan konfigurasi pada /etc/apache2/sites-available/000-default.conf
- Tambahkan konfigurasi port apache2
- Enable 000-default.conf kemudian restart service apache2, akses IP Eden dari client

**17. Karena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring "eden" akan diarahkan menuju eden.png**

- Tambahkan module rewrite pada .htaccess
- Tambahkan konfigurasi pada /etc/apache2/sites-available/eden.wise.e09.com.conf
- mudian enable module rewrite dan restart service apache2. Akses gambar dari client.
