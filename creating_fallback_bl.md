* After the fresh install, `/efi/EFI/BOOT/` will be having just one file `bootx64.efi` which is this is windows fallback bootloader.
  ![](https://raw.githubusercontent.com/itzzmesid/Arch-Linux-Installation/main/images/photo_2021-10-04_16-55-56.jpg)



* We need to put `shimx64.efi.signed` as `BOOTX64.EFI` here (ideally change folder to BOOT by deleting this folder first)
  and also put `mmx64.efi` and `fbx64.efi` here (no renaming).
* Extract `shimx64.efi.signed` from [here](http://mirrors.kernel.org/ubuntu/pool/main/s/shim-signed/shim-signed_1.45+15+1552672080.a4a1fbe-0ubuntu2_amd64.deb) 
* Extract `mmx64.efi` and `fbx64.efi` from [here](http://mirrors.kernel.org/ubuntu/pool/main/s/shim/shim_15+1552672080.a4a1fbe-0ubuntu2_amd64.deb)
* Copy paste the extracted files to `/efi/EFI/BOOT/`
  ![](https://raw.githubusercontent.com/itzzmesid/Arch-Linux-Installation/main/images/photo_2021-10-04_17-06-24.jpg)
  
* Do a `ls -lA /efi/EFI/BOOT/ /efi/EFI/refind/` and check it resembles this.
    ![](https://github.com/itzzmesid/Arch-Linux-Installation/blob/main/images/photo_2021-10-04_17-09-23.jpg)
    
* Also change the `/usr/share/libalpm/hooks/refind.hook` to below . Find the hook [here](https://github.com/itzzmesid/Arch-Linux-Installation/blob/main/dotfiles/usr/share/libalpm/hooks/refind.hook)
  ![](https://raw.githubusercontent.com/itzzmesid/Arch-Linux-Installation/main/images/photo_2021-10-04_17-24-40.jpg)
  
 * Now do a `sudo pacman -S refind`

* Add [BOOTX64.CSV](https://github.com/itzzmesid/Arch-Linux-Installation/blob/main/dotfiles/BOOTX64.CSV) to `/efi/EFI/refind/` 

  ### NOTE : Remember that refind.conf file has some changes every time refind gets update.This hook doesn't delete the `refind.conf-sample` file.Check what new      changes it has with some diff app (like Kompare) and merge the changes appropriately

* Once again do `ls -lA /efi/EFI/BOOT/ /efi/EFI/refind/`
![](https://raw.githubusercontent.com/itzzmesid/Arch-Linux-Installation/main/images/photo_2021-10-04_17-33-38.jpg)

* Reboot, turn secure boot on, delete the two refind entries
* If you have copied the CSV files properly, new entries will be created automatically

* Now enter ur BIOS and select `Add new boot entry`. Give it a name as fallback and point it to `\EFI\BOOT\BOOTX64.EFI`
* Reboot
* U will be greeted with blue screen of `rEFInd`. Just select `reset` from there.

* Add the hook, do a `pacman -S linux`(and linux-lts if u have installed lts kernel too) and turn on secure boot after that.
