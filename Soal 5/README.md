# konfigurasi dns
pada task ini melakukan konfigurasi dns server agar nantinya laptop dapat mengakses wordpress dan web sederhana di docker melalui domain. pastikan arahkan domain yang telah di konfigurasi ke ip haproxy, karena haproxy yang akan menangani trafik ke docker.
## DNS Server
1.konfigurasi zone dns sija.com

```nano /etc/bind/named.conf.local```
```
zone "sija.com" {
        type master;
        file "/etc/bind/db.sija.com";
};
```
2.copy template db.local menjadi db.sija.com <br>

```cp /etc/bind/db.local /etc/bind/db.sija.com```

3.konfigurasi domain sija.com <br>

```nano /etc/bind/db.sija.com```

```

;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     sija.com. root.sija.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      sija.com.
@       IN      A       172.18.251.2
wordpress       IN      A       172.18.251.3
web1    IN      A       172.18.251.3

```
4.konfigurasi resolv.conf <br>
```nano /etc/resolv.conf```
```
nameserver 172.18.251.2
```
5.restart bind9 <br>
```systemctl restart bind9```
