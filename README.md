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




### 1. alamat http://semerut18.pw
### 2. alias http://www.semeruyyy.pw
### 3. subdomain http://penanjakan.semeruyyy.pw yang diatur DNS-nya pada MALANG dan mengarah ke IP Server PROBOLINGGO
### 4. reverse domain untuk domain utama. Untuk mengantisipasi server dicuri/rusak,
### 5. DNS Server Slave pada MOJOKERTO
### 6. subdomain dengan alamat http://gunung.semeruyyy.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO.
