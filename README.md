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


## No 2
### Soal
dan Foosha sebagai DHCP Relay
### Penjelasan Jawaban

## No 3
### Soal
Ada beberapa kriteria yang ingin dibuat oleh Luffy dan Zoro, yaitu:
1. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server
2. Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169
### Penjelasan Jawaban

## No 4
### Soal
3. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50
### Penjelasan Jawaban

## No 5
### Soal
4. Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut
### Penjelasan Jawaban

## No 6
### Soal
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit
### Penjelasan Jawaban

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

## No 11
### Soal
Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie
### Penjelasan Jawaban

## No 12
### Soal
Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps
### Penjelasan Jawaban

## No 13
### Soal
Sedangkan, Zoro yang sangat bersemangat untuk mencari harta karun, sehingga kecepatan kapal Zoro tidak dibatasi ketika sudah mendapatkan harta yang diinginkannya
### Penjelasan Jawaban
