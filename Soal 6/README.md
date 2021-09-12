# Konfigurasi ACL
pada soal ini melakukan konfigurasi ACL agar laptop user tidak dapat terhubung ke docker server. tujuannya adalah agar laptop user tidak diizinkan langsung terhubung dengan docker server. nantinya laptop user akan mengakses docker server melalui haproxy.
## Router1
```
Router(config)access-list 100 deny ip host 192.168.1.10 host 172.18.251.4
Router(config)access-list 100 permit ip any any
Router(config)#int fa0/1
Router(config-if)#ip access-group 100 out
```
