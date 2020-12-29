# Lapres_Modul5_JA01
Oleh Kelompok A01
* _Wisnu (05111740000170)_
* _Salsabila Harlen (05111840000127)_

----------------------------------------------------------------
## Soal
* [Step A](#Step-A)
* [Step B](#Step-B)
* [Step C](#Step-C)
* [Step D](#Step-D)
* [Nomor 1](#Nomor-1)
* [Nomor 2](#Nomor-2)
* [Nomor 3](#Nomor-3)
* [Nomor 4](#Nomor-4)
* [Nomor 5](#Nomor-5)
* [Nomor 6](#Nomor-6)
* [Nomor 7](#Nomor-7)
--------------------------------------------------------------

## Prolog Soal

Setelah kalian mempelajari semua modul yang telah diberikan, Bibah ingin meminta bantuan untuk terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Bibah.

### Step A

Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Bibah seperti dibawah ini:

_**Jawab**_

Berikut ini merupakan topologi yang kami buat:

```
# Switch
uml_switch -unix switch0 > /dev/null < /dev/null & #A0
uml_switch -unix switch1 > /dev/null < /dev/null & #A1
uml_switch -unix switch2 > /dev/null < /dev/null & #A2
uml_switch -unix switch3 > /dev/null < /dev/null & #A3
uml_switch -unix switch4 > /dev/null < /dev/null & #A4
uml_switch -unix switch5 > /dev/null < /dev/null & #A5

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.72.9 eth1=daemon,,,switch0 eth2=daemon,,,switch3 mem=96M &
xterm -T BATU -e linux ubd0=BATU,jarkom umid=BATU eth0=daemon,,,switch3 eth1=daemon,,,switch2 eth2=daemon,,,switch5 mem=96M &
xterm -T KEDIRI -e linux ubd0=KEDIRI,jarkom umid=KEDIRI eth0=daemon,,,switch0 eth1=daemon,,,switch1 eth2=daemon,,,switch4 mem=96M &

# Server
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO,jarkom umid=PROBOLINGGO eth0=daemon,,,switch1 mem=128M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch1 mem=128M &
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &

# Klien
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch4 mem=96M &
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch5 mem=96M &

```

### Step B

karena kalian telah mempelajari Subnetting dan Routing, Bibah meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM.

_**Jawab**_
Berikut ini adalah link Google Docs Subnetting, Routing, dan Sintaks yang kami buat: [link](https://docs.google.com/document/d/13SG1cv-fEjutl0_ULIs_wWu55N2NcJYFy_IHwx6ry78/edit?usp=sharing)

### Step C

kalian juga diharuskan melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

_**Jawab**_

Berikut ini adalah syntax routing yang kami gunakan:

```
SURABAYA
ip route add 10.151.73.16/29 via 192.168.0.1
ip route add 192.168.1.0/24 via 192.168.0.1
ip route add 192.168.2.0/24 via 192.168.0.5
ip route add 192.168.0.8/29 via 192.168.0.5

KEDIRI: route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.1

BATU: route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.5
```

### Step D

Tugas berikutnya adalah memberikan ip pada subnet SIDOARJO dan GRESIK secara dinamis menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian kalian mengingat bahwa kalian harus setting DHCP RELAY pada router yang menghubungkannya, seperti yang kalian telah pelajari di masa lalu.

_**Jawab**_

DHCP Server di Mojokerto

DHCP Relay di Surabaya, Batu, dan Kediri


### Nomor 1

Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan MASQUERADE.

_**Jawab**_

Pada SURABAYA, buat file `1.sh` yang berisi syntax:

```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source 10.151.72.9
```

### Nomor 2

Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.

_**Jawab**_

Pada SURABAYA, buat file `2.sh` yang berisi syntax:

```
iptables -A FORWARD -d 10.151.73.16/29 -i eth0 -p tcp --dport 22 -j DROP
```

### Nomor 3

Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.

_**Jawab**_

Pada MALANG (DNS Server) dan MOJOKERTO (DHCP Server), buat file `3.sh` yang berisi syntax:

```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

### Nomor 4

Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat.

_**Jawab**_

Pada MALANG, buat file `4.sh` yang berisi syntax:

```
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.1.0/24 -j REJECT
```

### Nomor 5

Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya.

_**Jawab**_

Pada MALANG, buat file `5.sh` yang berisi syntax:

```
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 17:00 -j REJECT
```

### Nomor 6

Bibah ingin SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80.

_**Jawab**_

Pada SURABAYA, buat file `6.sh` yang berisi syntax:

```
iptables -A PREROUTING -t nat -p tcp -d 10.151.73.18 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.11:80
iptables -A PREROUTING -t nat -p tcp -d 10.151.73.18 --dport 80 -j DNAT --to-destination 192.168.0.10:80
```

### Nomor 7

Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop.

_**Jawab**_

Setiap UML yang memmiliki aturan DROP antara lain SURABAYA, MALANG, dan MOJOKERTO. Pada ketiga UML tersebut, buat file `7.sh` yang berisi syntax:

```
iptables -A -j Log --log-prefix "Dropped Connection: " --log-level 6
```
