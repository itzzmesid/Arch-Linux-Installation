* After the fresh install, `/efi/EFI/BOOT/` will be having just one file `bootx64.efi` which is this is windows fallback bootloader. 
* We need to put `shimx64.efi.signed` as `BOOTX64.EFI` here (ideally change folder to BOOT by deleting this folder first)
  and also put `mmx64.efi` and `fbx64.efi` here (no renaming).
* Extract `shimx64.efi.signed` from [here](http://mirrors.kernel.org/ubuntu/pool/main/s/shim-signed/shim-signed_1.45+15+1552672080.a4a1fbe-0ubuntu2_amd64.deb) 
* Extract `mmx64.efi` and `fbx64.efi` from [here](http://mirrors.kernel.org/ubuntu/pool/main/s/shim/shim_15+1552672080.a4a1fbe-0ubuntu2_amd64.deb)
* Copy paste the extracted files to `/efi/EFI/BOOT/`
