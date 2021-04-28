-> if using LAN, type in : ip link

-> ping archlinux.org (or any web) to see internet is available. 

-> timedatectl set-ntp true

# Disk Partitioning
* fdisk /dev/nvme0n1    `where nvme0n1 is my ssd drive`

# Formatting
* mkfs.ext4 -L arch /dev/nvme0n1p6
* fatlabel /dev/nvme1 ESP
Here I have labelled my root partition as `arch` & EFI partition as `ESP`

# Mounting the file systems
* mount /dev/nvme0n1p6 /mnt
* mkdir /mnt/efi
* mount /dev/nvme0n1p1 /mnt/efi
- Note: `n1p6 is the root partition` & `n1p1 is then default Windows 100 MB EFI partition`

# Installing the essential packages
* pacstrap /mnt base linux linux-firmware nano gvim

# fstab
* genfstab -L /mnt >> /mnt/etc/fstab  [-L ,since I have used label]
* Now open `/mnt/etc/fstab` 
    - Change the contents in between file system and last field to `defaults`
    - Example: `LABEL=data    /hdd    ext4    defaults    0 2 `
    
# chroot
-> arch-chroot /mnt

-> pacman -S ntfs-3g e2fsprogs dosfstools base-devel man-db man-pages texinfo systemd-swap pacman-contrib

# Time Zone

-> ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

-> hwclock --systohc

# Installing other necessary packages & configuring stuffs

-> sudo pacman -S --asdeps networkmanager crda bash-completion 

`exit chroot and enter again`

-> sudo pacman -S iptables-nft --asdeps And replace iptables with iptables-nft

Below is the way to configure `systemd-swap`. But I would recommend you to use `zram-generator`.

-> Now open `/etc/systemd/swap.conf`
    - Uncomment zswap_enabled=1 and set it to 0	
    - Uncomment zram_enabled=0 and set it to 1
    -
    -
    -

-> pacman -S firewalld --asdeps
-> 
