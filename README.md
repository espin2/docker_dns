# Environment
Server1  <br>
ip: 192.168.9.61 <br>
domain: sija.com

# install bind9 dan haproxy
```apt install haproxy bind9 -y```

# konfigurasi dns
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

6.konfigurasi haproxy <br>
```nano /etc/haproxy/haproxy.cfg```
```

frontend web
        bind *:80
        mode http
        acl acl_wordpress hdr(host) -i wordpress.sija.com
        acl acl_web1 hdr(host) -i web1.sija.com
        use_backend web_wordpress if acl_wordpress
        use_backend web_web1 if acl_web1

backend web_wordpress
        balance roundrobin
        server es-docker 192.168.9.61:8080 check

backend web_web1
        balance roundrobin
        server es-docker 192.168.9.61:8081 check
```

7.restart haproxy <br>
```systemctl restart haproxy```

8.Docker wordpress

```mkdir wordpress && cd wordpress``` <br>
```nano docker-compose.yaml```

```

services:
   db:
     image: mysql:latest
     volumes:
       - db:/var/lib/mysql
     restart: always
     environment:
         MYSQL_ROOT_PASSWORD: admin123
         MYSQL_DATABASE: wordpress
         MYSQL_USER: wordpress
         MYSQL_PASSWORD: admin123
   wordpress:
     depends_on:
       - db
     image: wordpress
     ports:
       - "8080:80"
     volumes:
       - wordpress:/var/www/html
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: admin123
       WORDPRESS_DB_NAME: wordpress

volumes:
  db:
  wordpress:

```
9.Dockerfile<br>
```mkdir myweb1 && cd myweb1``` <br>
```echo “Selamat Datang di Website Saya” >> index.html``` <br>

```nano Dockerfile``` <br>
```
FROM httpd:latest
WORKDIR /usr/local/apache2/htdocs
COPY index.html /usr/local/apache2/htdocs
```
```docker image build -t myweb1 .``` <br>
```docker images ls``` <br>
```docker run -d -p 8081:80 myweb1``` <br>
```docker-compose up -d``` <br>

# preqeusite ip
cisco
router1
```
Router>enable
Router#config t
Router(config)#int fa0/0
Router(config-if)#ip address 10.10.10.2 255.255.255.252
Router(config-if)#int fa0/1
Router(config-if)#ip address 172.18.251.1 255.255.255.248
```
router2
```
Router>enable
Router#config t
Router(config)#int fa0/0
Router(config-if)#ip address 10.10.10.1 255.255.255.252
Router(config-if)#int fa0/1
Router(config-if)#ip address 192.168.1.1 255.255.255.0
```
ip docker server <br>
```nano /etc/netplan/50-cloud-init.yaml```
```
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
      - 10.88.88.50/24
      gateway4: 10.88.88.1
      nameservers:
           addresses: [8.8.8.8, 1.1.1.1]
```
ip haproxy<br>
```nano /etc/netplan/50-cloud-init.yaml```
```
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
      - 10.88.88.50/24
      gateway4: 10.88.88.1
      nameservers:
           addresses: [8.8.8.8, 1.1.1.1]
```
ip dns server<br>
```nano /etc/netplan/50-cloud-init.yaml```
```
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
      - 10.88.88.50/24
      gateway4: 10.88.88.1
      nameservers:
           addresses: [8.8.8.8, 1.1.1.1]
```
# Routing OSPF

Router1 <br>
```
Router>enable
Router#config t
Router(config)#router ospf 1
Router(config-router)#network 10.10.10.0 0.0.0.3 area 0
Router(config-router)#network 172.18.251.0 0.0.0.7 area 0
```
Router2 <br>
```
Router>enable
Router#config t
Router(config)#router ospf 1
Router(config-router)#network 10.10.10.0 0.0.0.3 area 0
Router(config-router)#network 192.168.1.0 0.0.0.255 area 0
```

# ACL
Router 1
```
Router(config)access-list 100 deny ip host 192.168.1.10 host 172.18.251.4
Router(config)access-list 100 permit ip any any
Router(config)#int fa0/1
Router(config-if)#ip access-group 100 out


```

