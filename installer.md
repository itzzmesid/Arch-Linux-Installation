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

Below is the way to configure `zram-generator`:

    Create `/etc/systemd/zram-generator.conf`:

    ```
        # /etc/systemd/zram-generator.conf
        [zram0]
    ```

    

-> pacman -S firewalld --asdeps

-> systemctl enable firewalld

-> systemctl enable systemd-swap

-> systemctl enable systemd-resolved

-> systemctl enable NetworkManager

-> systemctl enable fstrim.timer

-> systemctl enable paccache.timer

-> `Outside chroot, type in **sudo ln -sf /var/run/systemd/resolve/stub-resolv.conf /mnt/etc/resolv.conf**`

-> Come back to `chroot`

-> Open /etc/mkinitcpio.conf

-> Replace `udev` with `systemd` in the `hooks array`.

-> Then do pacman -S linux linux-lts

# Localization

-> Open `/etc/locale.gen` and uncomment your choices. People from India can uncomment the same thing as I have shown below:

    - uncomment `en_US.UTF-8 UTF-8`,`en_GB.UTF-8 UTF-8`, `en_IN UTF-8`
 
-> Generate locales by running `locale-gen`

-> Create `locale.conf` and set the `LANG` variable according to your choice. I have created the below 2 entries in `locale.conf`

    - LANG=en_IN.UTF-8
    - LANGUAGE=en_IN:en_GB:en
   
# Network Configuration

-> Create the `hostname` file

    - Create `/etc/hostname`
    - Type in your hostname
    - Save and close it

-> Since both `systemd-resolved` & `nss-myhostname` can automatically do the **localhost name resolution**, there is no need to create `/etc/hosts` as mentioned in Wiki.

-> Open `nano /etc/config.d/wireless-regdom` and uncomment your region.

# Set up ROOT passwdord

-> passwd

# Setting up a new user named `sid`

-> useradd -m -G wheel sid

    - passwd sid and set the password for user `sid`
    - EDITOR=nano visudo (Uncomment wheel group)

# Installing a boot loader (rEFInd)

-> sudo pacman -S amd-ucode refind sbsigntools

-> Insert the usb which includes the `keys`     Note: Check `Keys` folder to get the keys

-> mount sdc1 in /mnt/keys                       Note: `sdc1` is the USB connected

-> refind-install --shim /keys/shim-expt/shimx64.efi --localkeys

-> Now open `/boot/refind_linux.conf`

    - "Boot with standard options"    "root=LABEL=arch quiet initrd=/boot/amd-ucode.img initrd=/boot/initramfs-%v.img"
    - "Boot with fallback initramfs"    "root=LABEL=arch quiet initrd=/boot/amd-ucode.img initrd=/boot/initramfs-%v-fallback.img"
    - "Boot to terminal"    "root=LABEL=arch systemd.unit=multi-user.target quiet initrd=/boot/amd-ucode.img initrd=/boot/initramfs-%v.img"
    - Save & exit.
-> Open `/efi/EFI/refind/refind.conf`

    - Line 229 - uncomment and set use_graphics_for osx,linux,windows
    - Line 422 - uncomment fold_linux_kernels false
    - Line 441 - uncomment extra_kernel_version_strings linux-lts,linux

-> Exit CHROOT

-> umount -R /mnt

-> Reboot

# Secure Boot enabling steps

Note that till now we were in `Secure Boot-OFF` mode. Now turn ON do the following:
-> Start MOKMAN

    - Select Key icon -> Enroll Key from disk->ESP -> EFI-> refind->keys->first thing-> (cer)

-> Boot the system

-> Login

# Installing the necessary drivers

-> sudo pacman -S nvidia nvidia-lts nvidia-prime opencl-nvidia mesa xf86-video-amdgpu vulkan-radeon libva-mesa-driver mesa-vdpau opencl-mesa ocl-icd opencl-nvidia

-> sudo pacman -S plasma dolphin konsole chromium

-> systemctl enable sddm









