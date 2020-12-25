# linux-lab-4
## Работа с RAID массивами с использованием mdadm

##### Ставим нужные пакеты:
```
sudo apt install mdadm
```
##### Создание loop-устройств
```
dd if=/dev/zero of=loopbackfile0.img bs=1M count=1000
dd if=/dev/zero of=loopbackfile1.img bs=1M count=1000
dd if=/dev/zero of=loopbackfile2.img bs=1M count=1000
dd if=/dev/zero of=loopbackfile3.img bs=1M count=1000
dd if=/dev/zero of=loopbackfile4.img bs=1M count=1000
sudo losetup -fP loopbackfile0.img
sudo losetup -fP loopbackfile1.img
sudo losetup -fP loopbackfile2.img
sudo losetup -fP loopbackfile3.img
sudo losetup -fP loopbackfile4.img
sudo losetup -a
```
![](images/1.png)
##### Создаём массив RAID10:
`mdadm --create /dev/md0 -l 10 -n 4 /dev/loop{18..21}`
![](images/2.png)
##### Проверяем состояние:
```
cat /proc/mdstat
mdadm --detail /dev/md0`
```
![](images/3.png)
##### Пометим диск как сбойный и извлечём его мз массива:
```
mdadm /dev/md0 --fail /dev/loop18 
mdadm /dev/md0 --remove /dev/loop18
cat /proc/mdstat
mdadm --detail /dev/md0
```
![](images/4.png)
##### Добавим чистый диск в массив взамен удалённого:
```
mdadm --add /dev/md0 /dev/loop22
cat /proc/mdstat
mdadm --detail /dev/md0
```
![](images/5.png)
##### Добавим ещё диск:
`mdadm --add /dev/md0 /dev/loop0`
##### Создадим конфигурационный файл:
```
sudo -i
mdadm --detail --scan > /etc/mdadm.conf
```
##### Остановим и запустим массив:
```
mdadm --stop /dev/md0
mdadm --assemble --scan
```
![](images/6.png)
##### Создаём раздел:
`fdisk /dev/md0`
![](images/7.png)
##### Создаём файловую систему:
`mkfs.ext4 /dev/md0p1`
##### Редактируем файл fstab
##### Выполнить монтирование:
`mount -a `
![](images/8.png)
