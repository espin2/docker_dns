# konfigurasi Routing OSPF
pada soal ini akan dilakukan konfigurasi routing OSPF agar setiap node dapat terhubung satu sama lain. pastikan laptop user bisa akses ke seluruh server.
## Router1
Router1 <br>
```
Router>enable
Router#config t
Router(config)#router ospf 1
Router(config-router)#network 10.10.10.0 0.0.0.3 area 0
Router(config-router)#network 172.18.251.0 0.0.0.7 area 0
```
## Router2
```
Router>enable
Router#config t
Router(config)#router ospf 1
Router(config-router)#network 10.10.10.0 0.0.0.3 area 0
Router(config-router)#network 192.168.1.0 0.0.0.255 area 0
```
