ตั้งค่าเพื่อให้สามารถ login ได้ อัตโนมัต
```cmd
SET PGPASSWORD=3245phawat!
```
```cmd
pg_dump -h 192.168.2.39 -U postgres -F t -b -d wow > btest.tar
```
```cmd
pg_restore -h 192.168.2.39 -U postgres -c -F t -d wow < btest.tar
```