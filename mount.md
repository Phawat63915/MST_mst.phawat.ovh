# SMB Protocol (For Windoes)
การใช้ cifs เพื่อให้ mount รองรับ SMB สำหรับ Windoes สำหรับที่ตั้งแชร์ไฟล์มาแล้วหรือ แชร์ด้วย SMB
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
#### 2.สร้างโฟเดอร์เพื่อจะได้อ้างอิงจากคำสั่งแชร์มาแทนที่โฟเดอร์นี้
```sh
sudo mkdir -p /path/my_mount_dir
```
#### 3.เชื่อมแชร์โฟเดอร์เข้ากับ Folder ในเครื่อง
[เพิ่มเติม cifs option](https://www.samba.org/~ab/output/htmldocs/manpages-3/mount.cifs.8.html)
```sh
sudo mount -t cifs -o "user=phawat" //192.168.1.39/server /path/my_mount_dir
```
```sh
sudo mount -t cifs -o "user=phawat,password=1111" //192.168.1.39/server /path/my_mount_dir
```
```sh
sudo mount -t cifs -o "domain=MYDOMAIN,username=MyUserName,password=myPas$werd,sec=ntlm,vers=3.0" //(your windows host ip)/(your remote share name) /mnt/my_mount_dir
```
####
## Remove Shere folder 
#### 1.ยกเลิกการแชร์โฟเดอร์
```sh
umount -l -f //192.168.1.39/server
```



# NFS Protocol (For Linux)
﻿Lab: NFS Sharing
- Linux NFS Server centOS7 คนแชร์ (192.168.56.101)
- Linux NFS Client centOS7 ลูกแชร์ (192.168.56.102)
- Windows client (Windows 10) ลูกแชร์ (192.168.56.1)
----------------------------------------------------
ติดตั้ง nfs service บน linux (ทั้ง Server และ Client) :
yum install -y nfs-utils nfs-utils-lib

Server Side
-----------
1.Check service nfs :
service nfs-server status

2.Allow firewalld port below :
firewall-cmd --permanent --add-port=111/tcp
firewall-cmd --permanent --add-port=54302/tcp
firewall-cmd --permanent --add-port=20048/tcp
firewall-cmd --permanent --add-port=2049/tcp
firewall-cmd --permanent --add-port=46666/tcp
firewall-cmd --permanent --add-port=42955/tcp
firewall-cmd --permanent --add-port=875/tcp

firewall-cmd --reload

3.สร้าง Shared Directory :
mkdir /var/shared
chmod 777 /var/shared

4.Create Export list on NFS (กำหนดลูกแชร์) :
vi /etc/exports
/var/shared	192.168.56.0/24(rw,sync,no_root_squash)

5.Restart NFS service :
service nfs-server restart

6.List shared export on server :
showmount -e 192.168.56.101

Client Side
-----------
1.สร้าง mount point สำหรับ mount shared และทำการ mount NFS
mkdir /var/client-shared
mount -t nfs 192.168.56.101:/var/shared/ /var/client-shared/

2.Verify mount 
df -h
mount

Mount NFS to Windows
--------------------
1.Start > Turn windows features on or off
2.Select service for NFS
3.CMD
mount 192.168.56.101:/var/shared S:\