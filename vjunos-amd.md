By default intel CPU's are only supported by vJunos images. In order to add AMD CPU support, perform the following steps:

1. Convert qcow2 to raw image

```bash
❯ qemu-img convert -pf qcow2 vJunos-switch-25.4R1.12.qcow2 -O raw vJunos-switch-25.4R1.12.raw
    (100.00/100%)
```

2.

```bash
sudo apt install -y kpartx
❯ sudo kpartx -vl vJunos-switch-25.4R1.12.raw 
loop17p1 : 0 32768 /dev/loop17 2048
loop17p2 : 0 66560000 /dev/loop17 34816
loop deleted : /dev/loop17

**Identify the P2 partition

❯ sudo kpartx -va vJunos-switch-25.4R1.12.raw 
add map loop17p1 (252:0): 0 32768 linear 7:17 2048
add map loop17p2 (252:1): 0 66560000 linear 7:17 34816

sudo mkdir -p /mnt/juniper

❯ sudo mount /dev/mapper/loop17p2 /mnt/juniper 

❯ mount | grep juniper
/dev/mapper/loop17p2 on /mnt/juniper type ext3 (rw,relatime)

cd /mnt/juniper/home/pfe/junos

❯ grep 'grep -ci vmx' start-junos.sh

CPU_FLAG=$(cat /proc/cpuinfo | grep -ci vmx)

❯ sudo sed -i 's:grep -ci vmx):grep -ci "vmx\\|svm"):' start-junos.sh

❯ grep svm start-junos.sh

CPU_FLAG=$(cat /proc/cpuinfo | grep -ci "vmx\|svm")

GNS3/images/QEMU                                                                                                                                                  
❯ sudo sync  

❯ sudo umount /mnt/juniper       

❯ sudo kpartx -vd vJunos-switch-25.4R1.12.raw 
del devmap : loop17p1
del devmap : loop17p2
loop deleted : /dev/loop17

```
