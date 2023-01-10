# SMB Protocol (For Windoes)
## Test Connext shere folder 
#### 1.ติดตั้ง packet
```sh
sudo apt smbclient install
```
#### 2.คำสั่งทดสอบ
##### 2.1 แบบที่ 1 คำสั่งทดสอบ <br>
`sudo smbclient //เขียนไรก็ได้มั่ง/ชื่อโฟเดอร์ -U ชื่อผูัใช้ -I ที่อยู่ไอพีหรือDomainแชร์ไฟล์`
```sh
sudo smbclient //test/server -U phawat -I 192.168.1.39
```
##### 2.2.แบบที่ 2 คำสั่งทดสอบ <br>
`sudo smbclient //ที่อยู่ไอพีหรือDomainแชร์ไฟล์/ชื่อโฟเดอร์/ -m SMB3 -U ชื่อผูัใช้`
```sh
sudo smbclient //192.168.1.39/server/ -m SMB3 -U phawat
```
## Create Shere folder 
#### 1.ติดตั้ง packet
```sh
sudo apt-get install cifs-utils
```
### 2.สร้างโฟเดอร์เพื่อจะได้อ้างอิงจากคำสั่งแชร์มาแทนที่โฟเดอร์นี้
```sh
sudo mkdir -p /path/my_mount_dir
```
####
[เพิ่มเติม cifs option](https://www.samba.org/~ab/output/htmldocs/manpages-3/mount.cifs.8.html)
```sh
sudo mount -t cifs -o "user=phawat" //192.168.1.39/server /path/my_mount_dir
```
####
## Remove Shere folder 
#### 1.ยกเลิกการแชร์โฟเดอร์
```sh
umount -l -f //192.168.1.39/server
```