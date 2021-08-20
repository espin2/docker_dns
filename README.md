# Environment
Server1  <br>
ip: 192.168.9.61 <br>
domain: espin.com

# install bind9 dan haproxy
```apt install haproxy bind9 -y```

# konfigurasi dns
1.konfigurasi zone dns espin.com

```nano /etc/bind/named.conf.local```
```
zone "espin.com" {
        type master;
        file "/etc/bind/db.espin.com";
};
```
2.copy template db.local menjadi db.espin.com <br>

```cp /etc/bind/db.local /etc/bind/db.espin.com```

3.konfigurasi domain espin.com <br>

```nano /etc/bind/db.espin.com```

```

;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     espin.com. root.espin.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      espin.com.
@       IN      A       192.168.9.61
www     IN      A       192.168.9.61
wordpress       IN      A       192.168.9.61
web1    IN      A       192.168.9.61
web2    IN      A       192.168.9.61
web3    IN      A       192.168.9.61

```
4.konfigurasi resolv.conf <br>
```nano /etc/resolv.conf```
```
nameserver 192.168.9.61
```
5.restart bind9 <br>
```systemctl restart bind9```

6.konfigurasi haproxy <br>
```nano /etc/haproxy/haproxy.cfg```
```

frontend web
        bind *:80
        mode http
        acl acl_wordpress hdr(host) -i wordpress.espin.com
        acl acl_web1 hdr(host) -i web1.espin.com
        acl acl_web2 hdr(host) -i web2.espin.com
        acl acl_web3 hdr(host) -i web3.espin.com
        use_backend web_wordpress if acl_wordpress
        use_backend web_web1 if acl_web1
        use_backend web_web2 if acl_web2
        use_backend web_web3 if acl_web3

backend web_wordpress
        balance roundrobin
        server es-docker 192.168.9.61:8080 check

backend web_web1
        balance roundrobin
        server es-docker 192.168.9.61:8081 check

backend web_web2
        balance roundrobin
        server es-docker 192.168.9.61:8082 check

backend web_web3
        balance roundrobin
        server es-docker 192.168.9.61:8083 check
```

7.restart haproxy <br>
```systemctl restart haproxy```
