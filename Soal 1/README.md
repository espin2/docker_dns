# prerequisite (konfigurasi IP Address)
Pada soal ini akan dilakukan untuk melakukan konfigurasi ip address pada setiap node. pastikan ip address sesuai pada soal yang telah diberikan
## Router 1
```
Router>enable
Router#config t
Router(config)#int fa0/0
Router(config-if)#ip address 10.10.10.2 255.255.255.252
Router(config-if)#int fa0/1
Router(config-if)#ip address 172.18.251.1 255.255.255.248
```
## Router 2
```
Router>enable
Router#config t
Router(config)#int fa0/0
Router(config-if)#ip address 10.10.10.1 255.255.255.252
Router(config-if)#int fa0/1
Router(config-if)#ip address 192.168.1.1 255.255.255.0
```
## Docker Server
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
## Haproxy Server
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
## DNS Server
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
