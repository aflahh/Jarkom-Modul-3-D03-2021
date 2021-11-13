# Jarkom-Modul-3-D03-2021

- Zahrotul Adillah (05111940000139)
- Muhammad Nur Abdurrauf (05111940000140)
- Aflah Hilmy (05111940000177)

## Daftar Isi

- [No 1](#no-1)
- [No 2](#no-2)
- [No 3](#no-3)
- [No 4](#no-4)
- [No 5](#no-5)
- [No 6](#no-6)
- [No 7](#no-7)
- [No 8](#no-8)
- [No 9](#no-9)
- [No 10](#no-10)
- [No 11](#no-11)
- [No 12](#no-12)
- [No 13](#no-13)

## No 1
### Soal
Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server
### Penjelasan Jawaban
Pertama, membuat topologi sama seperti di gambar soal
![image](https://user-images.githubusercontent.com/29938033/141612513-4229b86b-75ab-4c74-adde-f06bee707d68.png)

Jalankan command `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.193.0.0/16` di Foosha, kemudian tampilkan DNS yang digunakan Foosha dengan `cat /etc/resolv.conf`
![image](https://user-images.githubusercontent.com/29938033/141612674-351624b4-0817-4a68-bfb9-f8492f412459.png)

Isikan output command tadi ke file `/etc/resolv.conf` di EniesLobby, Jipangu, dan Water7 supaya bisa terhubung ke internet dengan command `echo nameserver 192.168.122.1`
![image](https://user-images.githubusercontent.com/29938033/141612792-c5007e7f-b4d7-4da3-82b8-e33e46a38c99.png)
*Contoh di EniesLobby*

Setelah itu, install bind9 di EniesLobby, isc-dhcp-server di Jipangu, dan squid di Water7 dengan command `apt-get update && apt-get install [nama package] -y`
![image](https://user-images.githubusercontent.com/29938033/141612913-71bcff69-dc8b-40da-8ff8-c26252dda7bb.png)
*DNS di EniesLobby*

![image](https://user-images.githubusercontent.com/29938033/141612961-239326f7-39e7-4b39-be52-19dd103c21ec.png)
*DHCP di Jipangu*

![image](https://user-images.githubusercontent.com/29938033/141612984-9ffec5b3-5151-43ee-afba-1091f4224dd1.png)
*Proxy di Water7*


## No 2
### Soal
dan Foosha sebagai DHCP Relay
### Penjelasan Jawaban
Install isc-dhcp-relay di Foosha dengan command `apt-get update && apt-get install isc-dhcp-relay -y`
![image](https://user-images.githubusercontent.com/29938033/141613242-7c9a42c8-10d1-4172-ab9f-505a18474f47.png)

Saat installasi selesai, akan ada prompt untuk mengisikan config dari relaynya. Isikan `192.193.2.4` sebagai server dhcp, `eth1 eth2 eth3` untuk interface, dan kosongi untuk additional options
![image](https://user-images.githubusercontent.com/29938033/141613293-a04e0e84-8ea1-4fc7-ad65-5d4c8db94aaa.png)

Secara automatis, file `/etc/default/isc-dhcp-relay` akan dibuat dan bisa dicek dengan command cat `cat /etc/default/isc-dhcp-relay`
![image](https://user-images.githubusercontent.com/29938033/141613484-a235c9be-76ab-46e1-90d8-aa1f6e00c67f.png)

## No 3
### Soal
Ada beberapa kriteria yang ingin dibuat oleh Luffy dan Zoro, yaitu:
1. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server
2. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
### Penjelasan Jawaban
1. Agar client menggunakan, isikan file `/etc/network/interfaces` di setiap client dengan
```
auto eth0
iface eth0 inet dhcp
```

![image](https://user-images.githubusercontent.com/29938033/141613588-0f512e98-6613-4df0-bec2-fa1d56eebc8c.png)
*Contoh di Loguetown*

2. Buatlah subnet untuk server ini dan juga untuk switch 1 dengan membuka file `/etc/dhcp/dhcpd.conf` di Jipangu dan diisi:
```
subnet 192.193.2.0 netmask 255.255.255.0 {
    option routers 192.193.2.4;
}

subnet 192.193.1.0 netmask 255.255.255.0 {
    range 192.193.1.20 192.193.1.99;
    range 192.193.1.150 192.193.1.169;
    option routers 192.193.1.1;
    option broadcast-address 192.193.1.255;
}
```

![image](https://user-images.githubusercontent.com/29938033/141614147-f176fc37-dd8e-4f16-aef0-ff11dfe22264.png)

File `/etc/default/isc-dhcp-server` tidak perlu diubah karena secara default, dhcp server akan melakukan listen pada eth0.

Tambahkan subnet switch 1 ke iptable dengan command `route add -net 192.193.1.0 netmask 255.255.255.0 gw 192.193.2.4` dan kemudian restart dhcp dengan `service isc-dhcp-server restart`

Maka ketika membuka client Loguetown:
![image](https://user-images.githubusercontent.com/29938033/141613895-89783e45-4a1a-4124-b5e7-350772fda1f7.png)
*Loguetown mendapatkan ip 192.193.1.22, sesuai dengan range yang ditentukan*


## No 4
### Soal
3. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
### Penjelasan Jawaban
Buat subnet untuk switch 3 dengan membuka kembali file `/etc/dhcp/dhcpd.conf` di Jipangu dan diisi:
```
subnet 192.193.3.0 netmask 255.255.255.0 {
    range 192.193.3.30 192.193.3.50;
    option routers 192.193.3.1;
    option broadcast-address 192.193.3.255;
}
```

Tambahkan subnet switch 1 ke iptable dengan command `route add -net 192.193.1.0 netmask 255.255.255.0 gw 192.193.2.4` dan kemudian restart dhcp dengan `service isc-dhcp-server restart`

Maka ketika client TottoLand direstart:
![image](https://user-images.githubusercontent.com/29938033/141614165-2fe3a751-31fb-42b5-ad74-1124e9736d56.png)
*TottoLand mendapatkan ip 192.193.3.30, sesuai dengan range yang ditentukan*

## No 5
### Soal
4. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut
### Penjelasan Jawaban
Buka kembali file `/etc/dhcp/dhcpd.conf` di Jipangu dan isikan baris dibawah untuk subnet switch1 dan switch3 dan restart dhcp server
```
option domain-name-servers 192.193.2.2;
```
![image](https://user-images.githubusercontent.com/29938033/141614235-8057a4a6-cf74-4759-942c-4662eb0b294b.png)

Agar dapat terhubung ke internet, buka file `/etc/bind/named.conf.options` di EniesLobby dan edit seperti berikut:
```
forwarders {
    192.168.122.1;
};

// dnssec-validation auto;
allow-query{any;};
```
![image](https://user-images.githubusercontent.com/29938033/141614313-036030a7-fb2b-41c0-9184-7d10b5d9dec4.png)

Restart dns server dengan command `service bind9 restart`

Untuk memastikan bahwa sudah berfungsi, lihat isi file `/etc/resolv.conf` di salah satu client
![image](https://user-images.githubusercontent.com/29938033/141614371-38236f4f-9824-4337-a697-1a67949b6f7a.png)
*Karena sudah ada `nameserver 192.193.2.2`, maka sudah berhasil*


## No 6
### Soal
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit
### Penjelasan Jawaban
Buka kembali file `/etc/dhcp/dhcpd.conf` di Jipangu dan tambahkan baris berikut ke subnet switch1:
```
default-lease-time 360;
max-lease-time 7200;
```
![image](https://user-images.githubusercontent.com/29938033/141614458-750f24fd-2e1f-4242-a10c-674289d65e0a.png)


Kemudian tambahkan juga baris berikut untuk subnet switch3:
```
default-lease-time 720;
max-lease-time 7200;
```
![image](https://user-images.githubusercontent.com/29938033/141614466-715cc224-813f-4394-916c-d70d18106d21.png)

Kemudian restart dhcp server.

Maka ketika membuka salah satu client di switch1:
![image](https://user-images.githubusercontent.com/29938033/141614515-0a3e2904-e2b9-4570-b0b7-641d5901059c.png)
*Loguetown mendapatkan ip 192.193.1.24 dengan lease time 360 detik atau 6 menit*

Dan ketika membuka salah satu client di switch2:
![image](https://user-images.githubusercontent.com/29938033/141614537-f1f1f80b-d275-4520-b71d-6206440c6efc.png)
*TottoLand mendapatkan ip 192.193.3.31 dengan lease time 720 detik atau 12 menit*


## No 7
### Soal
Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69
### Penjelasan Jawaban
1. Buka dan tambahkan script berikut : 
   ```
    host Skypie {
        hardware ethernet ce:f4:62:fe:3a:ef;  # hwaddress_milik_Skypie
        fixed-address 192.193.3.69;           # alamat IP tetap
    }
   ```
- hwaddress_milik_Skypie bisa didapat dari Skypie dengan command `ip a` <br><br>
![image](https://user-images.githubusercontent.com/72771774/141605949-20deab4a-d29f-4aa3-8657-5394f7849493.png)
<br><br>
2. Tambahkan konfigurasi `hwaddress ether ce:f4:62:fe:3a:ef` pada Skypie <br><br>
![image](https://user-images.githubusercontent.com/72771774/141606002-9973f76f-6df3-4135-8bda-e793ae7e867f.png) <br>
Bisa juga dengan menambahkannya pada `/etc/network/interfaces` di Skypie <br><br>
3. Restart Skypie <br><br>
4. Cek apakah alamat IP sudah tetap dan sesuai dengan format <br><br>
![image](https://user-images.githubusercontent.com/72771774/141606040-01e16528-62aa-44b7-b84e-b6b4acff24ad.png)

## No 8
### Soal
Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi. Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000
### Penjelasan Jawaban
1. Membuat domain pada DNS server yaitu EniesLobby
   - Menambahkan syntax berikut pada `named.conf.local`
     ```
     zone "jualbelikapal.d03.com" {
          type master;
          file "/etc/bind/jualbelikapal.d03.com";
     };
     ```
   - Mengedit file `jualbelikapal.d03.com` seperti di bawah ini : <br><br>
    ![image](https://user-images.githubusercontent.com/72771774/141606349-456a4f08-01f7-4613-ad98-d1b7a8be6b5f.png) <br>
   - Restart bind9 <br><br>
2. Pada Water7 install squid dan apache2 utils, aktifkan squid dan cek statusnya <br><br>
![image](https://user-images.githubusercontent.com/72771774/141606453-1c2215dc-9333-4a1d-b160-28035f90249e.png) <br> <br>
3. Backup file konfigurasi default yang disediakan squid dengan `mv /etc/squid/squid.conf /etc/squid/squid.conf.bak` <br><br>
4. Buat konfigurasi Squid baru Pada file `/etc/squid/squid.conf`
   ```
   http_port 'PORT_YANG_DIINGINKAN'
   visible_hostname 'NAMA_YANG_DIINGINKAN'
   ``` 
5. Jika ingin melakukan testing, tambahkan `http_access allow all` pada file squid yang telah dibuat

## No 9 ##
### Soal ###
Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy
### Penjelasan Jawaban ###
1. Pada Water7 install apache2 utils <br><br>
2. Buat password dengan `htpasswd -c /etc/squid/passwd 'USERNAME'`
   - Pada kasus ini digunakan : 
     ```
     htpasswd -cm /etc/squid/passwd luffybelikapald03
     htpasswd -m /etc/squid/passwd zorobelikapald03
     ```
   - -c merupakan create untuk file `/etc/squid/passwd`
   - -m merupakan command pada `htpasswd` untuk enkripsi MD5
   - Setelah menjalankan masing-masing command masukkan password sesuai keterangan pada soal yaitu **luffy_d03** untuk username **luffybelikapald03** dan **zoro_d03** untuk **zorobelikapald03**
   - Jika mau mengecek hasil enkripsi dapat dilihat pada file yang telah dibuat tadi <br><br>
   ![image](https://user-images.githubusercontent.com/72771774/141606878-6f459a80-9174-4fcb-b389-226b65499477.png)
   - Dapat dilihat bahwa `$apr1$` merupakan awalan dari enkripsi MD5 <br><br>
3. Edit konfigurasi squid menjadi : <br><br>
![image](https://user-images.githubusercontent.com/72771774/141606921-a97c1cd9-76a3-4198-924a-fb2886be3fad.png) <br> <br>
4. Restart squid <br><br>
5. Setting proxy pada client Loguetown
   ```export http_proxy="http://jualbelikapal.d03.com:5000"```
   - Jangan lupa install lynx
     ```
     apt-get update
     apt-get install lynx
     ``` 
6. Testing
   ```lynx http://its.ac.id```
   - Akan ada informasi proxy seperti ini <br><br>
   ![image](https://user-images.githubusercontent.com/72771774/141607117-2594d5ba-b901-487e-bb36-85f2ae26663f.png) <br>
   - Masukkan Username <br><br>
   ![image](https://user-images.githubusercontent.com/72771774/141607135-9ba339f0-0c9f-4706-b2fb-716f1cd2da02.png) <br>
   - Masukkan Password <br><br>
   ![image](https://user-images.githubusercontent.com/72771774/141607151-27b7363e-de36-4d56-95c5-1ca918a68ff7.png) <br>
   - Voila website http://its.ac.id telah terbuka
   ![image](https://user-images.githubusercontent.com/72771774/141607101-1c670b70-796c-41f5-9308-3567a45ec86a.png) <br>

## No 10
### Soal
Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)
### Penjelasan Jawaban
1. Di Water7, Buat file acl.conf di folder /etc/squid dengan isi sebagai berikut: 

![image](https://user-images.githubusercontent.com/74708771/141639592-3c9611b3-54ff-48fa-b1f8-6f22720b0e29.png)

2. Di dalam file /etc/squid/squid.conf tambahkan konfigurasi berikut:

![image](https://user-images.githubusercontent.com/74708771/141640605-eff6c6b9-3f40-416c-af46-6290c77d2e0d.png)

3. Restart service squid

![image](https://user-images.githubusercontent.com/74708771/141642112-7b881870-0075-4133-9b7d-244714251d48.png)

4. Testing (tampilan akses google.com dengan ```lynx google.com``` dari loguetown diluar jam akses)

![image](https://user-images.githubusercontent.com/74708771/141642181-64789418-b2ce-447e-9485-0ed9d472ddf8.png)

## No 11
### Soal
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie
### Penjelasan Jawaban
1. Di EniesLobby, tambahkan konfigurasi ke /etc/bind/named.conf.local seperti berikut:

![image](https://user-images.githubusercontent.com/74708771/141642385-5c0e6c87-ee01-4ab9-8a2d-459bfab5f549.png)

2. Buat folder baru ```mkdir /etc/bind/sunnygo``` lalu copy ```cp /etc/bind/db.local /etc/bind/sunnygo/super.franky.d03.com``` dan isi seperti berikut:

![image](https://user-images.githubusercontent.com/74708771/141642427-d778f2ab-b5ea-4cae-acb5-c56b95103f6b.png)

3. Restart service bind9

![image](https://user-images.githubusercontent.com/74708771/141642438-7b93434c-abb0-4ce4-85c2-aedc2da7655c.png)

4. Di Skypie, Lakukan ```apt-get update``` ```apt-get install apache2 php libapache2-mod-php7.0 -y``` ```apt-get install wget unzip -y```
5. Download file yang dibutuhkan dengan ```wget https://raw.githubusercontent.com/FeinardSlim/Praktikum-Modul-2-Jarkom/main/super.franky.zip``` lalu unzip ```unzip super.franky```

![image](https://user-images.githubusercontent.com/74708771/141642512-95b1a7e2-0808-4a81-b785-961a5b5f3bdb.png)

6. Copy ```cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/super.franky.d03.com.conf``` lalu isi seperti berikut:

![image](https://user-images.githubusercontent.com/74708771/141642521-4cd63923-0ede-47ba-844f-a584b5986338.png)

7. Buat folder baru ```mkdir /var/www/super.franky.d03.com``` dan copy file yang tadi di-download ```cp -r super.franky /var/www/super.franky.d03.com```

![image](https://user-images.githubusercontent.com/74708771/141642533-7dcf9356-15d4-4c62-b388-750a7d8589e9.png)

8. Start website dengan ```a2ensite super.franky.d03.com```

![image](https://user-images.githubusercontent.com/74708771/141642546-a17035d5-2e0e-431a-aeab-ba4b1e876470.png)

9. Restart service apache2

![image](https://user-images.githubusercontent.com/74708771/141642556-c1d04020-7afb-4640-852b-3c034467c19b.png)

10. Di Water7, tambahkan konfigurasi ke /etc/squid/squid.conf seperti berikut:

![image](https://user-images.githubusercontent.com/74708771/141642583-f21c5456-b664-47aa-8494-585de0f9e2bb.png)

11. Restart service squid

![image](https://user-images.githubusercontent.com/74708771/141642112-7b881870-0075-4133-9b7d-244714251d48.png)

12. Testing (```lynx google.com``` dari loguetown)

![image](https://user-images.githubusercontent.com/74708771/141643006-1ef25122-6b14-41dc-a760-f97ef0e96cd9.png)

## No 12
### Soal
Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps
### Penjelasan Jawaban
1. Di Water7, Buat file acl-bandwidth.conf di folder /etc/squid dengan isi sebagai berikut (konfigurasi termasuk no 13): 

![image](https://user-images.githubusercontent.com/74708771/141642750-3cfcc374-0c96-49fd-b5ed-36bac6f490cd.png)

2. Di dalam file /etc/squid/squid.conf tambahkan konfigurasi berikut:

![image](https://user-images.githubusercontent.com/74708771/141642698-4be55bd2-0cee-4734-984a-8f552b93729f.png)

3. Restart service squid

![image](https://user-images.githubusercontent.com/74708771/141642112-7b881870-0075-4133-9b7d-244714251d48.png)

4. Testing (download file gambar dari server dengan proxy luffy dari loguetown)(kecepatan 10kb/s diubah menjadi sekitar 1,25 KB/s)

![image](https://user-images.githubusercontent.com/74708771/141643268-cacd0e8a-b956-46c1-aa2b-4fa7dcbdbb8c.png)

## No 13
### Soal
Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya
### Penjelasan Jawaban
1. Di Water7, Buat file acl-bandwidth.conf di folder /etc/squid dengan isi sebagai berikut (konfigurasi sama dengan no 12): 

![image](https://user-images.githubusercontent.com/74708771/141642750-3cfcc374-0c96-49fd-b5ed-36bac6f490cd.png)

2. Restart service squid

![image](https://user-images.githubusercontent.com/74708771/141642112-7b881870-0075-4133-9b7d-244714251d48.png)

3. Testing (download file gambar dari server dengan proxy zoro dari loguetown)

![image](https://user-images.githubusercontent.com/74708771/141642972-ae5b3b96-a7d8-4807-babc-1dcb4496ce94.png)
