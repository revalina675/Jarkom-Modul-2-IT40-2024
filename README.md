# LAPORAN RESMI MODUL 02 KOMUNIKASI DATA DAN JARINGAN KOMPUTER
## Kelompok IT40
### Anggota Kelompok :
|             Nama              |     NRP    |
|-------------------------------|------------|
| Revalina Fairuzy Azhari Putri | 5027231001 |
| Kevin Anugerah Faza           | 5027231027 |


*Sebuah kerajaan besar di Indonesia sedang mengalami pertempuran dengan penjajah. Kerajaan tersebut adalah Sriwijaya. Karena merasa terdesak Sriwijaya meminta bantuan pada Majapahit untuk mempertahankan wilayahnya. Pertempuran besar tersebut berada di Nusantara. Untuk topologi lihat pada link ini*

# Soal 1
> Untuk mempersiapkan peperangan World War MMXXIV (Iya sebanyak itu), Sriwijaya membuat dua kotanya menjadi web server yaitu Tanjungkulai, dan Bedahulu, serta Sriwijaya sendiri akan menjadi DNS Master. Kemudian karena merasa terdesak, Majapahit memberikan bantuan dan menjadikan kerajaannya (Majapahit) menjadi DNS Slave.

Topologi Jaringan

![image](https://github.com/user-attachments/assets/6f28664b-96c3-4dbb-b081-396f692a0383)

**Network Config.**

**Nusantara (Router)**
```
auto eth0
iface eth0 inet dhcp
        up iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

auto eth1
iface eth1 inet static
	address 10.83.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.83.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.83.3.1
	netmask 255.255.255.0
```
**Client**
* Samaratungga
```
auto eth0
iface eth0 inet static
	address 10.83.1.2
	netmask 255.255.255.0
	gateway 10.83.1.1
```

* Mulawarman
```
auto eth0
iface eth0 inet static
	address 10.83.3.2
	netmask 255.255.255.0
	gateway 10.83.3.1
```

* AlexanderVolta
```
auto eth0
iface eth0 inet static
	address 10.83.3.3
	netmask 255.255.255.0
	gateway 10.83.3.1
  up echo nameserver 10.83.3.5 > /etc/resolv.conf
  up echo nameserver 10.83.2.2 > /etc/resolv.conf
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

* Balaraja
```
auto eth0
iface eth0 inet static
	address 10.83.3.4
	netmask 255.255.255.0
	gateway 10.83.3.1
```
**Solok (Load Balancer)**
```
auto eth0
iface eth0 inet static
	address 10.83.1.3
	netmask 255.255.255.0
	gateway 10.83.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
**Server**

* Sriwijaya
```
auto eth0
iface eth0 inet static
	address 10.83.3.5
	netmask 255.255.255.0
	gateway 10.83.3.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

* Majapahit
```
auto eth0
iface eth0 inet static
	address 10.83.2.2
	netmask 255.255.255.0
	gateway 10.83.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

**Web Server**
* Kotalingga
```
auto eth0
iface eth0 inet static
	address 10.83.3.6
	netmask 255.255.255.0
	gateway 10.83.3.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

* Bedahulu
```
auto eth0
iface eth0 inet static
	address 10.83.2.4
	netmask 255.255.255.0
	gateway 10.83.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

* Tanjungkulai
```
auto eth0
iface eth0 inet static
	address 10.83.2.3
	netmask 255.255.255.0
	gateway 10.83.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

Setelah membuat semua konfigurasi untuk network yang ada, sambungkan local network dengan Nusantara menggunakan command : 
`iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.168.0.0/16`

Lakukan ping apakah clients telah terintegrasi dengan NAT dan Local Network
![image](https://github.com/user-attachments/assets/3723c787-ced7-4085-b2bb-96ced85eb595)

# Soal 2
> Karena para pasukan membutuhkan koordinasi untuk melancarkan serangannya, maka buatlah sebuah domain yang mengarah ke Solok dengan alamat sudarsana.xxxx.com dengan alias www.sudarsana.xxxx.com, dimana xxxx merupakan kode kelompok. Contoh: sudarsana.it01.com.

Buat sebuah script pada DNS Master untuk menyimpan konfigurasi sudarsana.xxxx.com yang diminta, yaitu :
```
#!/bin/bash

# Buat domain sudarsana.it40.com
echo 'zone "sudarsana.it40.com" {
	type master;
	file "/etc/bind/jarkom/sudarsana.it40.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/sudarsana.it40.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     sudarsana.it40.com. sudarsana.it40.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      sudarsana.it40.com.
@       IN      A       10.83.3.6     ; IP Kotalingga
www     IN      CNAME   sudarsana.it40.com.' > /etc/bind/jarkom/sudarsana.it40.com

service bind9 restart
```

# Soal 3
> Para pasukan juga perlu mengetahui mana titik yang akan diserang, sehingga dibutuhkan domain lain yaitu pasopati.xxxx.com dengan alias www.pasopati.xxxx.com yang mengarah ke Kotalingga.

Buat lagi sebuah script pada DNS Master untuk menyimpan konfigurasi pasopati yang diminta, yaitu :
```
#!/bin/bash

# Buat domain pasopati.it40.com
echo 'zone "pasopati.it40.com" {
	type master;
	file "/etc/bind/jarkom/pasopati.it40.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/pasopati.it40.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it40.com. root.pasopati.it40.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      pasopati.it40.com.
@       IN      A       10.83.3.6     ; IP Kotalingga
www     IN      CNAME   pasopati.it40.com.' > /etc/bind/jarkom/pasopati.it40.com

service bind9 restart
```

# Soal 4
> Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi dan suplai meme terbaru tersebut mengarah ke Tanjungkulai dan domain yang ingin digunakan adalah rujapala.xxxx.com dengan alias www.rujapala.xxxx.com.

Buat lagi sebuah script untuk menyimpan konfigurasi rujapala yang diminta, yaitu :
```
#!/bin/bash

# Buat domain rujapala.it40.com
echo 'zone "rujapala.it40.com" {
	type master;
	file "/etc/bind/jarkom/rujapala.it40.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/rujapala.it40.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     rujapala.it40.com. rujapala.it40.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      rujapala.it40.com.
@       IN      A       10.83.3.6     ; IP Kotalingga
www     IN      CNAME   rujapala.it40.com.' > /etc/bind/jarkom/rujapala.it40.com

service bind9 restart
```
# Soal 5
> Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Nusantara.

Testing ping ke semua domain yang telah dibuat 
![image](https://github.com/user-attachments/assets/adcec7ff-97c3-404f-9ec7-2d73e77faeb5)

# Soal 6
> Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain pasopati.xxxx.com melalui alamat IP Kotalingga (Notes: menggunakan pointer record).

Pada DNS Master lakukan konfigurasi dengan melakukan pengubahan pada named.conf.local dan in-addr.arpa seperti pada konfigurasi berikut (membalik IP server Sriwijaya, yang awalnya `10.83.3' menjadi '3.83.10') :
```
#!/bin/bash

# Buat reverse DNS (Record PTR)
echo 'zone "3.83.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.83.10.in-addr.arpa";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/3.83.10.in-addr.arpa

echo ';
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it40.com. root.pasopati.it40.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
   86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.83.10.in-addr.arpa.    IN      NS      pasopati.it40.com.
3                       IN      PTR     pasopati.it40.com.' > /etc/bind/jarkom/3.83.10.in-addr.arpa

service bind9 restart
```

Setelah itu pada masing-masing client (Samaratungga, Mulawarman, AlexanderVolta, Balaraja) lakukan setup konfigurasi 
```
# Set nameserver ke IP Nusantara
echo 'nameserver 192.168.122.1' > /etc/resolv.conf

# Install utils
apt-get update
apt-get install dnsutils -y

# Kembalikan nameserver ke Sriwijaya and Majapahit
echo '
nameserver 10.83.3.5
nameserver 10.83.2.2' > /etc/resolv.conf
```

Seusai semua berhasil terkonfigurasi, lakukan testing pada salah satu client 
![image](https://github.com/user-attachments/assets/ab53550e-2e66-4479-bb76-58c53bcd027f)

# Soal 7
> Akhir-akhir ini seringkali terjadi serangan brainrot ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Majapahit untuk semua domain yang sudah dibuat sebelumnya yang mengarah ke Sriwijaya.

Pada DNS Server (Sriwijaya) lakukan setup konfigurasi 
```
#!/bin/bash

# Tambahkan kebutuhan untuk menjadi master pochinki
echo '
zone "sudarsana.it40.com" {
        type master;
        notify yes;
        also-notify { 10.83.2.2; }; //IP Majapahit
        allow-transfer { 10.83.2.2; }; //IP Majapahit
        file "/etc/bind/jarkom/sudarsana.it40.com";
};

zone "pasopati.it40.com" {
        type master;
        notify yes;
        also-notify { 10.83.2.2; }; //IP Majapahit
        allow-transfer { 10.83.2.2; }; //IP Majapahit
        file "/etc/bind/jarkom/pasopati.it40.com";
};

zone "rujapala.it40.com" {
        type master;
        notify yes;
        also-notify { 10.83.2.2; }; //IP Majapahit
        allow-transfer { 10.83.2.2; }; //IP Majapahit
        file "/etc/bind/jarkom/rujapala.it40.com";
};' > /etc/bind/named.conf.local

service bind9 restart
```

Lalu lakukan setup konfigurasi juga pada DNS Slave (Majapahit) 
```
#!/bin/bash
# Cek apakah bind9 sudah terinstal
if ! command -v named &> /dev/null
then
    echo "Bind9 belum terinstal, melakukan instalasi..."
    # Melakukan instalasi bind9
    apt-get update
    apt-get install bind9 -y
else
    echo "Bind9 sudah terinstal."
fi

echo '
zone "sudarsana.it40.com" {
    type slave;
    masters { 10.83.3.5; }; // IP Sriwijaya
    file "/var/lib/bind/sudarsana.it40.com";
};

zone "pasopati.it40.com" {
    type slave;
    masters { 10.83.3.5; }; // IP Sriwijaya
    file "/var/lib/bind/pasopati.it40.com";
};

zone "rujapala.it40.com" {
 type slave;
    masters { 10.83.3.5; }; // IP Sriwijaya
    file "/var/lib/bind/rujapala.it40.com";
};' > /etc/bind/named.conf.local

service bind9 restart
```
Setelah itu lakukan ping testing dengan menggunakan `ping pasopati.it40.com' pada masing-masing client 7-6
