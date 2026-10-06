**1 . Saída deste comando dig @localhost xunta.gal**
```
dig @localhost xunta.gal

; <<>> DiG 9.20.29-1~deb13u1-Debian <<>> @localhost xunta.gal
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33597
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 3c61185b70c8d6a5010000006ac4cb964b23ac9d0f50d457 (good)
;; QUESTION SECTION:
;xunta.gal.			IN	A

;; ANSWER SECTION:
xunta.gal.		28134	IN	A	85.91.64.109

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Tue Oct 06 10:21:10 UTC 2026
;; MSG SIZE  rcvd: 82
```

**2 . Contido do ficheiro /etc/bind/named.conf.options**
```
options {
        directory "/var/cache/bind";
        forwarders {
                192.168.20.10;
        };
        dnssec-validation auto;

        listen-on-v6 { any; };
};
```
**Saída deste comando: dig @localhost santiagodecompostela.gal**
```
dig @localhost santiagodecompostela.gal

; <<>> DiG 9.20.29-1~deb13u1-Debian <<>> @localhost santiagodecompostela.gal
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8435
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 408aafe5b22c40d6010000006ac4cbea3cc4d3d4113a76ed (good)
;; QUESTION SECTION:
;santiagodecompostela.gal.	IN	A

;; ANSWER SECTION:
santiagodecompostela.gal. 300	IN	A	195.57.25.148

;; Query time: 13 msec
;; SERVER: 127.0.0.1#53(localhost) (UDP)
;; WHEN: Tue Oct 06 10:22:34 UTC 2026
;; MSG SIZE  rcvd: 97
```
**3 . Pega no documento de entrega o contido do arquivo de zona, e do arquivo /etc/bind/named.conf.local**

**Arquivo /etc/bind/named.conf.local**
```
zone "starwars.lan" {
type primary;
file "/etc/bind/zonas/db.starwars.lan";
};
zone "20.168.192.in-addr.arpa" {
        type primary;
        file "/etc/bind/zonas/db.192";
};
```
**Zona db.starwars.lan**
```
$TTL    604800
@       IN      SOA     darthvader.starwars.lan samuel.starwars.lan.(

                        1 ; Serial
                        604800 ; Refresh
                        86400 ; Retry
                        2419200 ; Expire
                        604800 ) ; Negative Cache TTL
;   Servidores de nome
@       IN      NS      darthvader.starwars.lan.
@       IN      NS      darthsidious.starwars.lan.
;   Rexistros A dos servidores de nomes
darthvader      IN      A       192.168.20.10
darthsidious        IN      A       192.168.20.11
; Rexistros A principais
skywalker       IN      A       192.168.20.101
skywalker       IN      A       192.168.20.111
luke        IN      A       192.168.20.22
yoda        IN      A       192.168.20.24
yoda        IN      A       192.168.20.25
c3p0        IN      A       192.168.20.26
;   Alias (CNAME)
palpatine       IN      CNAME       darthsidious.starwars.lan
;   Rexistro MX (servidor de correo)
@       IN      MX      10      c3p0.starwars.lan
;
lenda       IN      TXT     "Que a forza te acompanhe"
```
**4 . Pega no documento de entrega o contido do arquivo de zona, e do arquivo /etc/bind/named.conf.local**

**Zona db.192**
```
$TTL 604800
@       IN      SOA         darthvader.starwars.lan. samuel.starwars.lan.(

                            1 ; Serial
                            604800 ; Refresh
                            86400 ; Retry
                            2419200 ; Expire
                            604800 ) ; Negative Cache TTL

;
@       IN      NS      darthvader.starwars.lan.
@       IN      NS      darthsidious.starwars.lan.
10         IN      PTR         darthvader.starwars.lan.
101         IN      PTR         skywalker.starwars.lan.
111         IN      PTR         skywalker.starwars.lan.
22         IN      PTR         luke.starwars.lan.
11         IN      PTR         darthsidious.starwars.lan.
24         IN      PTR         yoda.starwars.lan.
25         IN      PTR         yoda.starwars.lan.
26         IN      PTR         c3p0.starwars.lan.
```
**Arquivo /etc/bind/named.conf.local**
```
zone "starwars.lan" {
type primary;
file "/etc/bind/zonas/db.starwars.lan";
};
zone "20.168.192.in-addr.arpa" {
        type primary;
        file "/etc/bind/zonas/db.192";
};
```
**5 . Comproba que podes resolver os distintos rexistros de recursos. Pega no documento de entrega a saída dos comandos:**

- **nslookup darthvader.starwars.lan localhost**
```
nslookup darthvader.starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

Name:	darthvader.starwars.lan
Address: 192.168.20.10
```
- **nslookup skywalker.starwars.lan localhost**
```
nslookup skywalker.starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

Name:	skywalker.starwars.lan
Address: 192.168.20.101
Name:	skywalker.starwars.lan
Address: 192.168.20.111
```
- **nslookup starwars.lan localhost**
```
nslookup starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

*** Can't find starwars.lan: No answer
```
- **nslookup -q=mx starwars.lan localhost**
```
nslookup -q=mx starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

starwars.lan	mail exchanger = 10 c3p0.starwars.lan.starwars.lan.
```
- **nslookup -q=ns starwars.lan localhost**
```
nslookup -q=ns starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

starwars.lan	nameserver = darthvader.starwars.lan.
starwars.lan	nameserver = darthsidious.starwars.lan.
```
- **nslookup -q=soa starwars.lan localhost**
```
nslookup -q=soa starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

starwars.lan
	origin = darthvader.starwars.lan.starwars.lan
	mail addr = samuel.starwars.lan
	serial = 1
	refresh = 604800
	retry = 86400
	expire = 2419200
	minimum = 604800
```
- **nslookup -q=txt lenda.starwars.lan localhost**
```
nslookup -q=txt lenda.starwars.lan localhost
Server:		localhost
Address:	127.0.0.1#53

lenda.starwars.lan	text = "Que a forza te acompanhe"
```
- **nslookup 192.168.20.11 localhost**
```
nslookup 192.168.20.11 localhost
11.20.168.192.in-addr.arpa	name = darthsidious.starwars.lan.
```




