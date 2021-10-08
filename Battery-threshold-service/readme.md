# This is a systemd service to limit battery charging inorder to increase the life of battery (ASUS)

  * Copy this service to `/etc/systemd/system`

  ### Note
  `echo 80 > /sys/class/power_supply/BAT1/charge_control_end_threshold'`
   
   Here 80 is the threshold.You can set it to anything you want. BAT1 represents the battery.

  * After copying, start the service by `systemctl start battery-charge-threshold.service`
  * In future incase you edit this file, don't forget to  do `systemctl daemon-reload`


See Wiki [https://wiki.archlinux.org/title/Laptop/ASUS#Battery_charge_threshold]
