# Jarkom-Modul-3-D04-2021

1. Daffa Muhamad Azhar [05111940000037]
2. Taqarra Rayhan I [05111940000121]
3. Iwan Dwi Prakoso [05111940000229]

# MODUL 3

![topology](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/topology.PNG)

## Nomor 1

Buat EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server.

### Jawab
Pertama membuat Topologi seperti gambar diatas.

untuk settingan DHCP Server (Jipangu) :

![1-dhcp](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/1-dhcp.PNG)

untuk settingan Proxy Server :

## Nomor 2

Buat Foosha sebagai DHCP Relay.

### Jawab
Untuk membuat Foosha sebagai DHCP Relay dapat dilakukan sebagai berikut :

![2-relay](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/2-relay.PNG)

## Nomor 3

Client yang melalui Switch1 mendapatkan range IP dari 10.23.1.20 - 10.23.1.99 dan 10.23.1.150 - 10.23.1.169.

### Jawab
untuk yang nomor 3 yang perlu diatur konfigurasinya adalah dengan mengatur konfigurasi pada `/etc/dhcp/dhcpd.conf` lalu mengatur subnet pada 10.23.1.0 di option-domain-name-server menjadi
seperti berikut
![jipangu setting](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/jipangu-setting.PNG)

## Nomor 4

Client yang melalui Switch3 mendapatkan range IP dari 10.23.3.30 - 10.23.3.50.

### Jawab
untuk yang nomor 4 yang perlu diatur konfigurasinya adalah dengan mengatur konfigurasi pada `/etc/dhcp/dhcpd.conf` lalu mengatur subnet pada 10.23.3.0 di option-domain-name-server menjadi
seperti berikut

![jipangu setting no 4](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/jipangu-setting.PNG)
## Nomor 5

Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut.

### Jawab
 pada nomer 5 hal yang perlu dilakukan adalah mengubah settingan pada `/etc/bind/named.conf.options` dan menambahkan forwaders pada settingan menjadi seperti berikut
![jipangu setting no 4](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/enieslobby-internet.PNG)
   
## Nomor 6

Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.

### Jawab
bisa diliat pada konfigurasi pada `/etc/dhcp/dhcpd.conf` pada default-lease time dan max lease time dan mengatur sesuai seperti pada soal

![jipangu setting no 6](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/jipangu-setting.PNG)

## Nomor 7

Skypie sebagai server untuk jual - beli dengan alamat IP 10.23.3.69 (tetap).

### Jawab
caranya adalah dengan membuat host skypie pada jipangu dalam file `/etc/dhcp/dhcpd.conf` dan untuk hardwareaddress dapat dengan `ip a` pada skypie dan menjadi seperti berikut	


![jipangu setting no 6](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/jipangu-setting.PNG)
## Nomor 8

Loguetown digunakan sebagai client Proxy. Proxy harus bisa diakses dengan nama **jualbelikapal.d04.com** dengan port = 5000.

### Jawab

Pada water 7 sebagai proxy server diset nameserver ke IP address Foosha terlebih dahulu agar bisa terhubung ke internet untuk mendownload squid dan apache2-utils. Ini dibutuhkan untuk membuat proxy server. Selanjutnya setelah download selesai, backup configurasi squid ke file baru untuk berjaga-jaga jika config diperlukan lagi nantinya.

```sh
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install squid -y
apt-get install apache2-utils
mv /etc/squid/squid.conf /etc/squid/squid.conf.bak
```

Setelah itu, buat config squid baru. Di dalam config tersebut diisikan configurasi port yang menerima request ke proxy server, nama hostname, dan untuk semua request diset allow access terlebih dahulu.

```
echo 'http_port 5000
visible_hostname jualbelikapal.d04.com
http_access allow ALL

' > /etc/squid/squid.conf
```

## Nomor 9

Proxy dipasang authentication user proxy dengan encryption MD5 dengan 2 username, yaitu `luffybelikapald04` dengan pass `luffy_d04` serta `zorobelikapald04` dengan pass `zoro_d04`.

### Jawab

Authentication dilakukan dengan menggunakan apache2-utils yang sudah diinstall sebelumnya. Untuk mengeset username dan password dieksekusi perintah berikut. Saat mengeksekusi perintah, console akan meminta input berupa password dari username yang dimasukkan

```sh
htpasswd -c /etc/squid/passwd luffybelikapald04  # type password luffy_d04
htpasswd /etc/squid/passwd zorobelikapald04 # type password zoro_d04
```

Selanjutnya dilakukan perubahan configurasi, jika sebelumnya configurasi masih diset allow untuk semua request, sekarang saat user akan menggunakan proxy akan diminta authentikasi berupa username dan password.

```sh
echo 'http_port 5000
visible_hostname jualbelikapal.d04.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS

' > /etc/squid/squid.conf
```

Diminta mengisi credential saat menggunakan proxy

![username](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/9-username.PNG)

Saat mengisi username

![username-isi](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/9-username-isi.PNG)

Saat mengisi password

![password](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/9-password.PNG)

## Nomor 10

Hanya dapat diakses setiap **SENIN - KAMIS 07.00 - 11.00** dan **SELASA - JUMAT 17.00 - 03.00 KEESOKAN HARINYA**.

### Jawab

Untuk melakukan configurasi waktu kapan saja user dapat menggunakna proxy, dibuat file baru acl.conf dan disiikan peraturan sebagai berikut. S, M, T, W, H, F, A adalah kode untuk hari yang dimulai dari minggu (Sunday) berurutan sampai Sabtu (sAturday). Karena waktu akses selasa sampai jumat pada pukul 17.00 sampai 03.00 pagi keesokan hariku, configurasi pada acl.conf dipisah harinya.

```sh
echo 'acl waktu time MTWH 07:00-11:00
acl waktu time WHFA 00:00-03:00
acl waktu time TWHF 17:00-24:00' > /etc/squid/acl.conf
```

Selanjutnya configurasi diatas diimportkan ke file configurasi dari squid dengan cara "include path_to_acl_file". Pada baris terbawah file configurasi juga ditambahkan jika squid akan allow user yang terautentikasi pada waktu yang telah ditentukan, tetapi akan menolak jika diakses pada waktu selain itu.

```sh
echo 'include /etc/squid/acl.conf
http_port 5000

visible_hostname jualbelikapal.d04.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS waktu

http_access deny all

' > /etc/squid/squid.conf
```

Setiap mengakses proxy akan tetap diminta authentikasi

![authentication](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/10-authentication-alert.PNG)

Jika diakses saat diluar waktu akan memunculkan forbidden, access denied

![forbidden](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/10-forbidden.PNG)
![forbidden](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/10-access-denied.PNG)

## Nomor 11

Setiap mengakses `google.com` akan di-redirect ke `super.franky.d04.com` (di Node Skypie) dengan website yang sama pada soal shift modul 2.

### Jawab

Untuk meredirect jika user mengakses google.com maka dilakukan configurasi sebagai berikut.

```sh
echo '

include /etc/squid/acl.conf
http_port 5000

visible_hostname jualbelikapal.d04.com

auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Login
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
acl google dstdomain google.com
http_access deny google
deny_info http://super.franky.d04.com/ google
http_access allow USERS waktu
http_access deny all

' >/etc/squid/squid.conf
```

Saat mengakses google akan diredirect

![redirect](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/11-redirect.PNG)

Redirect mengarah ke super.franky.d04.com yang memiliki content sebagai berikut

![webserver-content](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/11-isi-webserver.PNG)

## Nomor 12

Mendapatkan gambar pada `super.franky.d04.com` dan melihatnya dengan kecepatan **10kbps**.

### Jawab

TODO: belum ada penjelasan

Saat siap untuk mendownload gambar

![ready-to-download](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/12-ready-download.PNG)

Saat mendownload gambar kecepatan luffy berkisar di 10KB per detik

![10kbps](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/12-10kbps.PNG)

## Nomor 13

untuk zoro, kecepatan tidak dibatasi.

### Jawab

TODO: belum ada penjelasan

Saat zorro mendownload gamabr maka gambar maka download akan langsung selesai dikarenakan kecepatan tidak dibatasi dan ukuran gambar juga kecil. Berikut adalah hasil download gambar dari zorro

![zorro-result](https://github.com/azhar416/Jarkom-Modul-3-D04-2021/blob/main/img/13-zorro-hasil.PNG)
