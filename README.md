# Jarkom-Modul-5-E01-2022      
### Laporan Resmi Pengerjaan Sesi Lab Jaringan Komputer     
        
#### Anggota:
-   5025201002 - Tegar Ganang Satrio Priambodo
-   5025201042 - Cindi Dwi Pramudita


## Soal Modul 5
Setelah kalian mempelajari semua modul yang telah diberikan, Loid ingin meminta bantuan untuk terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Loid.     
1. Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Loid dibawah ini:
Keterangan : 	      
        	Eden adalah DNS Server
		WISE adalah DHCP Server
		Garden dan SSS adalah Web Server
		Jumlah Host pada Forger adalah 62 host
		Jumlah Host pada Desmond adalah 700 host
		Jumlah Host pada Blackbell adalah 255 host
		Jumlah Host pada Briar adalah 200 host

2. Untuk menjaga perdamaian dunia, Loid ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM setelah melakukan subnetting.
3. Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
4. Tugas berikutnya adalah memberikan ip pada subnet Forger, Desmond, Blackbell, dan Briar secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

## Jawaban Modul 
### Perhitungan VLSM
Berikut adalah topologi dan pembagian subnet

![image](https://user-images.githubusercontent.com/87058985/206841528-44cfc435-2b84-4065-8d7a-ca6a6ae71e97.png)

Berikut adalah Tree yang sudah kita buat

![image](https://user-images.githubusercontent.com/87058985/206841553-47620664-2205-4fe7-996e-5320a3000c01.png)

Berikut adalah Perhitungannya

![image](https://user-images.githubusercontent.com/87058985/206841604-87d90ab8-9e55-4b65-8f35-55a381eb82e6.png)

Menggunakan pembagian IP dengan teknik VLSM. Berikut merupakan tabel hasil pembagian IP

![image](https://user-images.githubusercontent.com/87058985/206857533-1bee877f-9753-46f3-9d78-30aedc113ef9.png)


### Konfigurasi Network setiap node
[ Westalis ]
```
auto eth0
iface eth0 inet static
	address 10.22.7.146
	netmask 255.255.255.252
auto eth1
iface eth1 inet static
	address 10.22.7.129
	netmask 255.255.255.248
auto eth2
iface eth2 inet static
	address 10.22.7.1
	netmask 255.255.255.128
auto eth3
iface eth3 inet static
	address 10.22.0.1
	netmask 255.255.252.0

```

[ Strix ]
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.22.7.145
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 10.22.7.149
	netmask 255.255.255.252

```

[ Ostania ]
```
auto eth0
iface eth0 inet static
	address 10.22.7.150
	netmask 255.255.255.252
auto eth1
iface eth1 inet static
	address  10.22.7.137
	netmask 255.255.255.248
auto eth2
iface eth2 inet static
	address  10.22.4.1
	netmask 255.255.254.0
auto eth3
iface eth3 inet static
	address  10.22.6.1
	netmask 255.255.255.0

```
[ Eden ]
```
auto eth0
iface eth0 inet static
	address 10.22.7.130
	netmask 255.255.255.248
        gateway 10.22.7.129


```
[ WISE ]
```
auto eth0
iface eth0 inet static
	address 10.22.7.131
	netmask 255.255.255.248
        gateway 10.22.7.129
```

[ Garden ]
```
auto eth0
iface eth0 inet static
	address 10.22.7.138
	netmask 255.255.255.248
        gateway 10.22.7.137
```

[ SSS ]
```
auto eth0
iface eth0 inet static
	address 10.22.7.139
	netmask 255.255.255.248
        gateway 10.22.7.137
```
[ Forger ]
```
auto eth0
iface eth0 inet dhcp
```
[ Desmond ]
```
auto eth0
iface eth0 inet dhcp
```
[ Blackbell ]
```
auto eth0
iface eth0 inet dhcp
```
[ Briar ]
```
auto eth0
iface eth0 inet dhcp
```


### Routing dan Konfigurasi DNS, Web server, DHCP Server, dan DHCP relay
[ Strix ]
```
route add -net 10.22.7.0 netmask 255.255.255.128 gw 10.22.7.146 
route add -net 10.22.0.0 netmask 255.255.252.0 gw 10.22.7.146 
route add -net 10.22.7.128 netmask 255.255.255.248 gw 10.22.7.146 

route add -net 10.22.4.0 netmask 255.255.254.0 gw 10.22.7.150
route add -net 10.22.6.0 netmask 255.255.255.0 gw 10.22.7.150 
route add -net 10.22.7.136 netmask 255.255.255.248 gw 10.22.7.150
```
[ Westalis ]
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.22.7.145
```
[ Ostania ]
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.22.7.149
```
[ Eden adalah DNS Server ] 

Pada File > /etc/bind/named.conf.options
```
apt update
apt install bind9 -y
echo '
options {
        directory "/var/cache/bind";
        forwarders {
                192.168.122.1;
        };
        allow-query { any; };
        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
```
Kemudian lakukan Restart Dengan `service bind9 restart`  

[  WISE adalah DHCP Server  ]     

Pada File  > /etc/default/isc-dhcp-server     

```
apt update
apt install isc-dhcp-server -y
echo '
INTERFACES="eth0" ' > /etc/default/isc-dhcp-server
```

Pada File > /etc/dhcp/dhcpd.conf

```
echo '
ddns-update-style none;
option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;
default-lease-time 600;
max-lease-time 7200;
log-facility local7;
subnet 10.22.0.0 netmask 255.255.252.0 {
    range 10.22.0.2 10.22.3.254;
    option routers 10.22.0.1;
    option broadcast-address 10.22.3.255;
    option domain-name-servers 10.22.7.130;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 10.22.7.0 netmask 255.255.255.128 {
    range 10.22.7.2 10.22.7.126;
    option routers 10.22.7.1;
    option broadcast-address 10.22.7.127;
    option domain-name-servers 10.22.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}
subnet 10.22.4.0 netmask 255.255.254.0 {
    range 10.22.4.2 10.22.5.254;
    option routers 10.22.4.1;
    option broadcast-address 10.22.5.255;
    option domain-name-servers 10.22.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}
subnet 10.22.6.0 netmask 255.255.255.0 {
    range 10.22.6.2 10.22.6.254;
    option routers 10.22.6.1;
    option broadcast-address 10.22.6.255;
    option domain-name-servers 10.22.7.130;
    default-lease-time 720;
    max-lease-time 7200;
}
subnet 10.22.7.128 netmask 255.255.255.248 {}
subnet 10.22.7.144 netmask 255.255.255.252 {}
subnet 10.22.7.148 netmask 255.255.255.252 {}
subnet 10.22.7.136 netmask 255.255.255.248 {}
‘  > /etc/dhcp/dhcpd.conf
``` 
Lakukan Restart Dengan `service isc-dhcp-server restart`

[ Ostania sebagai DHCP Relay ]
```
apt update
apt install isc-dhcp-relay -y
echo '
SERVERS="10.22.7.131"
INTERFACES="eth2 eth3 eth1 eth0"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
service isc-dhcp-relay restart
```

[ Westalis sebagai DHCP Relay ]
```
apt update
apt install isc-dhcp-relay -y
echo '
SERVERS="10.22.7.131"
INTERFACES="eth2 eth3 eth0 eth1"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
service isc-dhcp-relay restart
```

[Garden dan sss adalah Web Server  ]
```
apt update
apt install apache2 -y
service apache2 start
echo "$HOSTNAME" > /var/www/html/index.html
```

## Soal Nomor 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.


### Jawaban Nomor 1
[ Strix ]
```
IPETH0="$(ip -br a | grep eth0 | awk '{print $NF}' | cut -d'/' -f1)"
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source "$IPETH0" -s 10.22.0.0/21
```

## Soal Nomor 2
Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

### Jawaban Nomor 2
[ Strix ]
```
iptables -A FORWARD -d 10.22.7.131 -i eth0 -p tcp --dport 80 -j DROP
iptables -A FORWARD -d 10.22.7.131 -i eth0 -p udp --dport 80 -j DROP
```

## Soal Nomor 3
Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

### Jawaban Nomor 3
[  WISE ]
Reject bila terdapat PING ICMP Lebih dari 2
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP
```
[ Eden ]     
Reject bila terdapat PING ICMP Lebih dari 2
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP
```
[ Forger ]

![image](https://user-images.githubusercontent.com/87058985/206855330-9e421e17-cc26-4e73-9185-6027750f833e.png)

[ Briar ]

![image](https://user-images.githubusercontent.com/87058985/206855368-01f4bc12-369c-4223-89d7-14e02cf79014.png)

[ Blackbell ]

![image](https://user-images.githubusercontent.com/87058985/206855387-add97edd-0ad2-409b-b530-2915f3e98ca3.png)


## Soal Nomor 4
Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

### Jawaban Nomor 4    
```
iptables -A INPUT -m time --weekdays Sat,Sun -j REJECT
iptables -A INPUT -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -m time --timestart 16:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT

iptables -A INPUT -s 10.22.0.0/22 -m time --weekdays Sat,Sun -j REJECT
iptables -A INPUT -s 10.22.0.0/22 -m time --timestart 00:00 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
iptables -A INPUT -s 10.22.0.0/22 -m time --timestart 16:01 --timestop 23:59 --weekdays Mon,Tue,Wed,Thu,Fri -j REJECT
```
Testing Hari Sabtu 10 Desember 2022 pukul 10.00

![image](https://user-images.githubusercontent.com/87058985/206855421-afc061ad-9b75-4da8-9e3d-3ea07521242e.png)

Hari Jumat 9 Desember 2022 Pukul 09.00

![image](https://user-images.githubusercontent.com/87058985/206855439-4acd0879-5f11-41dc-8669-05b3756d1239.png)


## Soal Nomor 5
Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

### Jawaban Nomor 5
Pertama, konfigurasi untuk masing-masing node dengan port untuk request masing-masing node adalah 80 dan 443 dengan menggunakan --dport karena saat terjadi request akan terdistribus antara SSS & Garden. Untuk mendistribusikan maka menggunakan --every 2 dan diarahkan pada node sasaran distribusi dengan --to-destination.

[ Garden ]
```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.22.7.138 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.22.7.138:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.22.7.138 -j DNAT --to-destination 10.22.7.139:80]
[SSS}
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.22.7.139 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.22.7.139:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.22.7.139 -j DNAT --to-destination 10.22.7.138:443
```

## Soal Nomor 6
Karena Loid ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

### Jawaban Nomor 6
```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```
## Kendala
Banyak :v 

