-> Use iwctl to connect to wireless networks

```ini
# ping archlinux.org
``` 
->To check if we have internet access.

```ini
# timedatectl set-ntp true
```

# Disk Partitioning

```ini
# fdisk /dev/nvme0n1
``` 
->where nvme0n1 is an ssd drive I want to install.

# Formatting
```ini
# mkfs.ext4 -L arch /dev/nvme0n1p6
``` 
->p6 is the partition where I want to install the /root directory
```ini 
# fatlabel /dev/nvme1 ESP
``` 
->make sure NOT TO format this EFI partition since it contains Windows EFI files.

->Here I have labelled my root partition as `arch` & EFI partition as `ESP`

# Mounting the file systems
```ini
# mount /dev/nvme0n1p6 /mnt
# mkdir /mnt/efi
# mount /dev/nvme0n1p1 /mnt/efi
```
->Note: `n1p6 is the root partition` & `n1p1 is then default Windows 100 MB EFI partition`

# Installing the essential packages
```ini
# pacstrap /mnt base linux linux-firmware nano gvim
```

# fstab
```ini
# genfstab -L /mnt >> /mnt/etc/fstab
```
[-L ,since I have used label]
-> OPTIONAL: Now open `/mnt/etc/fstab` 
                - Change the contents in between file system and last field to `defaults`
                - Example: `LABEL=data    /hdd    ext4    defaults    0 2 `
    
# chroot into the new /
```ini
# arch-chroot /mnt
```

```ini
# pacman -S ntfs-3g e2fsprogs dosfstools base-devel man-db man-pages texinfo pacman-contrib
```
-> These are essential packages for file systems
```ini
# pacman -S zram-generator
```

# Setting the time zone
```ini
# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
```

```ini
# hwclock --systohc
```

# Installing other necessary packages & configuring stuffs

```ini
# sudo pacman -S --asdeps networkmanager crda bash-completion firewalld
```

-> Exit `chroot` and again `arch-chroot`

```ini
# sudo pacman -S iptables-nft --asdeps And replace iptables with iptables-nft
```

### Below is the way to configure `zram-generator`:
    
```ini
Create /etc/systemd/zram-generator.conf and insert the below value:
[zram0]
```

# Enabling the services

```ini
# systemctl enable firewalld
# systemctl enable systemd-resolved
# systemctl enable NetworkManager
# systemctl enable fstrim.timer
# systemctl enable paccache.timer
```

`Outside chroot, type in **sudo ln -sf /var/run/systemd/resolve/stub-resolv.conf /mnt/etc/resolv.conf**`

-> Come back to `chroot`

-> OPTIONAL : Open `/etc/mkinitcpio.conf`
                - Replace `udev` with `systemd` in the `hooks array`
                = Then do pacman -S linux linux-lts

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

-> Since both `systemd-resolved` & `nss-myhostname` can automatically do the localhost name resolution, there is no need to create a separate `/etc/hosts` file

-> Open `nano /etc/conf.d/wireless-regdom` and uncomment your region.

# Set up ROOT password

```ini
# passwd
```
# Setting up a new user named `sid`
```ini
# useradd -m -G wheel sid
```
```ini
# passwd sid 
```
and set the password for user `sid`
```ini
# EDITOR=nano visudo (Uncomment wheel group)
```

# Installing a boot loader (rEFInd)

```ini
# sudo pacman -S amd-ucode refind sbsigntools
```

-> Insert the usb which includes the `efi files`     
<!-- Note: Check [`Shim-Files` folder to get the efi files](https://github.com/itzzmesid/Arch-LinuxInstallation/tree/main/ShimFiles) -->
Check #Shim-Files for shim files

```ini
# mount sdc1 /mnt/shimfiles
```
NOTE: `sdc1` is the USB connected and `shimfiles` is the folder which holds the files. Choosing where and how to store is your choice.

```ini
refind-install --shim /shimfiles/shimx64.efi --localkeys
```

-> Open `/boot/refind_linux.conf` & add in the following

    - "Boot with standard options"    "root=LABEL=arch quiet initrd=/boot/amd-ucode.img initrd=/boot/initramfs-%v.img"
    - "Boot with fallback initramfs"    "root=LABEL=arch quiet initrd=/boot/amd-ucode.img initrd=/boot/initramfs-%v-fallback.img"
    - "Boot to terminal"    "root=LABEL=arch systemd.unit=multi-user.target quiet initrd=/boot/amd-ucode.img initrd=/boot/initramfs-%v.img"
    - Save & exit.

-> Open `/efi/EFI/refind/refind.conf`

    - Line 229 - uncomment and set use_graphics_for osx,linux,windows
    - Line 422 - uncomment fold_linux_kernels false
    - Line 441 - uncomment extra_kernel_version_strings linux-lts,linux

-> Exit CHROOT

```ini
# umount -R /mnt
```

-> Reboot

# Secure Boot enabling steps

Note that till now we were in `Secure Boot-OFF` mode. Now turn ON do the following:
-> Start MOKMAN

    - Select Key icon -> Enroll Key from disk->ESP -> EFI-> refind->keys->first thing-> (cer)

-> Boot the system

-> Login

# Installing the necessary drivers

```ini
# sudo pacman -S nvidia nvidia-lts nvidia-prime opencl-nvidia mesa xf86-video-amdgpu vulkan-radeon libva-mesa-driver mesa-vdpau opencl-mesa ocl-icd opencl-nvidia

# sudo pacman -S plasma dolphin konsole chromium

# systemctl enable sddm
```

-> Reboot and Log In. 

Enjoy arch linux =)

---

# Uninstalling Arch Linux
1. Install mokutil tool and run the following command:`sudo mokutil --reset`. This will unenroll the mok.Then reboot.
2. You will be then greeted with a blue screen which asks for password to start the  unenroll process. Just complete it.
3. Go inside the efi folder in `/`
4. Delete all the folders except `Microsoft`
5. Now boot to windows
6. Delete the `ext4 partitions`
7. Run the following command : `bcdboot C:\Windows` in cmd as admin

---

ALSO CHECK HOW TO ENABLE FALLBACK BOOTLOADER [here](https://github.com/itzzmesid/Arch-Linux-Installation/blob/main/creating_fallback_bl.md)





