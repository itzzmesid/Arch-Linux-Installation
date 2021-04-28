if using LAN->ip link

timedatectl set-ntp true
---
# Disk Partitioning
* fdisk /dev/nvme0n1
---
# Formatting
* mkfs.ext4 -L arch /dev/nvme0n1p6
* fatlabel /dev/nvme1 ESP
---
# Mounting the file systems
* mount /dev/nvme0n1p6 /mnt
* mkdir /mnt/efi
* mount /dev/nvme0n1p1 /mnt/efi
- Note: `n1p6 is the root partition` & `n1p1 is then default Windows 100 MB EFI partition`
---
# Installing the essential packages
* pacstrap /mnt base linux linux-firmware nano gvim
---
# fstab
* genfstab -L /mnt >> /mnt/etc/fstab    [L since I have used label]
* Now open `/mnt/etc/fstab` 
    - 
