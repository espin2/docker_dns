# konfigurasi haproxy
pada soal ini melakukan konfigurasi haproxy agar seluruh trafik docker server harus melalui haproxy.saat laptop user ingin akses ke docker server bisa melalui domain yang telah di definisikan di setingan haproxy dibawah ini yaitu wordpress.sija.com dan web1.sija.com
## Haproxy Server

1.konfigurasi haproxy <br>
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

2.restart haproxy <br>
```systemctl restart haproxy```
