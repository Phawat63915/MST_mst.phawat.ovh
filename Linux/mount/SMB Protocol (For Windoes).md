# SMB Protocol (For Windoes)
การใช้ cifs เพื่อให้ mount รองรับ SMB สำหรับ Windoes สำหรับที่ตั้งแชร์ไฟล์มาแล้วหรือ แชร์ด้วย SMB
## Test Connext shere folder 
#### 1.ติดตั้ง packet
```sh
sudo apt-get update
sudo apt-get upgrade
sudo apt-get smbclient install
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
sudo umount -l -f //192.168.1.39/server
```
## Fix mount folder missing after restart
จากการใช้คำสั่งด้านบน หาก Restart Server mount จะถูกยกเลิกและหายไป วิธีการแก้ทำตามขั้นตอนนี้
#### 1.ให้เปิดแก้ไขไฟล์
```bash
sudo nano /etc/fstab 
```
#### 2.เพิ่มบรรทัดด้านล่าง ไปที่ `/etc/fstab`
`//ที่อยู่แชร์ไฟล์/โฟเดอร์แชร์ /ที่อยู่ภายในเครื่องที่จะเมาร์ cifs username=ชื่อผู้ใช้,password=รหัส`
```fstab
//192.168.56.1/server_docker /root/phawatv cifs username=phawat,password=1111
```