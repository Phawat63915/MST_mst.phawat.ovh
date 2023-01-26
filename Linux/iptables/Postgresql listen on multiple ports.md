#### เพิ่ม
```sh
iptables -t nat -A PREROUTING -p tcp -d 192.168.56.15 --dport 5425:5431 -j DNAT --to-destination 172.17.0.1:5432
```
#### ลบ
```sh
iptables -t nat -D PREROUTING -p tcp -d 192.168.56.15 --dport 5425:5431 -j DNAT --to-destination 172.17.0.1:5432
```