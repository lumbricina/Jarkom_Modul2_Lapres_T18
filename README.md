# Jarkom_Modul2_Lapres_T18
- M. Reza Aisyi 05311840000036
- Agnes Lesmono 05311840000044
----------------------------------
## Soal Shift
### Persiapan UML 
Siapkan Xming dan Putty terlebih dahulu, untuk t18 ip yang digunakan sebagai berikut :
- IP_eth0_SURABAYA_tiap_kelompok = 10.151.70.62
- IP_tuntap_tiap_kelompok = 10.151.70.61
- IP_eth1_SURABAYA_tiap_kelompok = 10.151.71.121
- IP_MALANG_tiap_kelompok = 10.151.71.122
- IP_MOJOKERTO_tiap_kelompok = 10.151.71.123
- IP_PROBOLINGGO_tiap_kelompok = 10.151.71.124

1. Setelah login, buat file script dengan ekstensi .sh yang akan digunakan untuk menyimpan script membuat router, switch, dan klien. Misalkan kita membuat file bernama **topologi.sh.**
2. isi dari topologi.sh adalah sebagai berikut :
```
# Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.70.61 eth1=daemon,,,switch2 eth2=daemon,,,switch1 mem=96M &

# Server
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T PROBOLINGGO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &

# Klien
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=96M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=96M &
```




#### 1. alamat http://semerut18.pw
kita akan membuat domain semerut18.pw
Lakukan perintah pada MALANG. Isikan seperti berikut: <br/>
```nano /etc/bind/named.conf.local``` <br/>
Isikan configurasi domain jarkom2020.com sesuai dengan syntax berikut: <br/>
```
zone "semerut18.pw" {
	type master;
	file "/etc/bind/jarkom/semerut18.pw";
};
```
![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/DNS%20SERVER%20MALANG.PNG)

#### 2. alias http://www.semerut18.pw
Record CNAME adalah sebuah record yang membuat alias name dan mengarahkan domain ke alamat/domain yang lain. <br/>
buka file semerut18.pw pada server malang dan isinya seperti ini  <br/>
![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/nomor%202.PNG) <br/>
pada bagian ``` @	IN	NS	semerut18.pw```

#### 3. subdomain http://penanjakan.semerut18.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
Edit file ``` /etc/bind/jarkom/semerut18.pw ``` lalu tambahkan subdomain untuk semerut18.pw yang mengarah ke IP PROBOLINGGO. <br/>
pada bagian ``` penanjakan	IN	A	10.151.71.124 ; IP PROBOLINGGO``` <br/>
![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/nomor%202.PNG)

#### 4. reverse domain untuk domain utama. Untuk mengantisipasi server dicuri/rusak,
- Edit file ``` /etc/bind/named.conf.local ``` pada MALANG 
- Lalu tambahkan konfigurasi berikut ke dalam file named.conf.local
```
zone "71.151.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/71.151.10.in-addr.arpa";
};
```

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/DNS%20SERVER%20MALANG.PNG)

untuk mengecek konfigurasi kita gunakan ```host -t PTR "IP MALANG"``` pada GRESIK <br/>
![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/reverse.PNG)

#### 5. DNS Server Slave pada MOJOKERTO
Edit file /etc/bind/named.conf.local di MALANG 
```
zone "semerut18.pw" {
    type master;
    notify yes;
    also-notify { "10.151.71.123"; }; // Masukan IP MOJOKERTO tanpa tanda petik
    allow-transfer { "10.151.71.123"; }; // Masukan IP MOJOKERTO tanpa tanda petik
    file "/etc/bind/jarkom/semerut18.pw";
};
```

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/DNS%20SERVER%20MALANG.PNG)

<br/> Edit file /etc/bind/named.conf.local di MOJOKERTO

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/dns%20slave%202.PNG)

<br/> Kemudian restart bind9, matikan MALANG dan test dari GRESIK

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/dns%20slave%204.PNG)

#### 6. subdomain dengan alamat http://gunung.semeruyyy.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO.

Pada MALANG /etc/bind/jarkom/semerut18.pw ubah menjadi berikut (maaf lupa screenshot)

```
...
;
@           IN  NS  semerut18.pw.
@           IN  A       10.151.71.124
www         IN  CNAME   10.151.73.124
penanjakan  IN  A       10.151.71.124
ns1         IN  A       10.151.71.124
gunung      IN  NS      ns1
```

Edit /etc/bind/named.conf.options pada MALANG dan MOJOKERTO 
- comment (tambahin //) di dnssec-validation auto
- tambahkan ```allow-query{any;};```

Edit /etc/bind/named.conf.local di MALANG

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/sebelom%20ping%20gunungsemeru%201.PNG)

Edit di MOJOKERTO /etc/bind/delegasi/gunung.semerut16.pw menjadi seperti ini

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/sebelom%20ping%20gunungsemeru%202.PNG)

Coba PING dari gresik

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/ping%20gunungsemeru.PNG)

Yeay bisa :)

#### 7. subdomain dengan nama http:naik.gunung.semeruyyy.pw, domain ini diarahkan ke IP Server PROBOLINGGO

Pada MOJOKERTO ubah file /etc/bind/delegasi/gunung.semerut18.pw menjadi seperti berikut

~[](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/otw%20naik%20gunung%20semeru.PNG)

Edit juga file /etc/bind/named.conf.local seperti berikut

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/otw%20naik%20gunung%20semeru%202.PNG)

Coba ping dari gresik

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/nyampe%20naek%20gunung%20semeru.PNG)

#### 8. Domain http://semeruyyy.pw memiliki *DocumentRoot* pada /var/www/semeruyyy.pw. Awalnya web dapat diakses menggunakan alamat http://semeruyyy.pw/index.php/home.

Menambahkan ServerName dan DocumentRoot di PROBOLINGGO file semerut18.pw.conf

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/web%20semerut18.pw%20bts.PNG)

Buka browser, masukkin *semerut18.pw*

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/web%20semerut18.pw.PNG)

Tadaa


#### 9. Karena dirasa alamat urlnya kurang bagus, maka diaktifkan mod rewrite agar urlnya menjadi http://semeruyyy.pw/home

Aktifkan a2enmod rewrite di PROBOLINGGO

Edit /etc/apache2/sites-enabled/semerut18.pw.conf -> ganti `AllowOverride None` jadi `AllowOverride All`

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/otw%20semeru%20home%202.PNG)

Edit .htaccess seperti berikut

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/otw%20semeru%20home.PNG)

Buka browser dan jalankan semerut18.pw/home
Tadaa

![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/semeru.pw%20home.PNG)

#### 10. Web http://penanjakan.semeruyyy.pw akan digunakan untuk menyimpan assets file yang memiliki *DocumentRoot* pada /var/www/penanjakan.semeruyyy.pw dan memiliki struktur folder sebagai berikut
```
/var/www/penanjakan.semeruyyy.pw
    /public/javascripts
    /public/css
    /public/images/errors
```

Ubah /etc/apache2/sites-enabled/penanjakan.semerut18.pw.conf menjadi seperti berikut


![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/otw%20penanjakan1.PNG)

Kemudian buka di browser dan tampak tampilan seperti berikut
![](https://github.com/lumbricina/Jarkom_Modul2_Lapres_T18/blob/main/IMAGES/penanjakan.PNG)