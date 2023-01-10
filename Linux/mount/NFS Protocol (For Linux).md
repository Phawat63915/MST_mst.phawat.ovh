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