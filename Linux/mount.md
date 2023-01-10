# SMB Protocol (For Windoes)
## Test Connext 
#### 1.ติดตั้ง packet
```sh
sudo apt smbclient install
```
#### 2.1 แบบที่ 1 คำสั่งทดสอบ <br>
`sudo smbclient //เขียนไรก็ได้มั่ง/ชื่อโฟเดอร์ -U ชื่อผูัใช้ -I ที่อยู่ไอพีหรือDomainแชร์ไฟล์`
```sh
sudo smbclient //test/server -U phawat -I 192.168.1.38
```
#### 2.2.แบบที่ 2 คำสั่งทดสอบ <br>
`sudo smbclient //ที่อยู่ไอพีหรือDomainแชร์ไฟล์/ชื่อโฟเดอร์/ -m SMB3 -U ชื่อผูัใช้`
```sh
sudo smbclient //192.168.1.38/server/ -m SMB3 -U phawat
```