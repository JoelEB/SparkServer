###Setting up Raspberry Pi Serial Port For Atlas Scientific Stamps 

####Disable Serial Port Login

To enable the serial port for your own use you need to disable login on the port. 
There are two files that need to be edited. The first and main one is /etc/inittab
This file has the command to enable the login prompt and this needs to be disabled. 
Edit the file and scroll down to the end. You will see a line similar to:

```T0:23:respawn:/sbin/getty -L ttyAMA0 115200 vt100```

Disable it by adding a \# character to the beginning. Save the file.

`#T0:23:respawn:/sbin/getty -L ttyAMA0 115200 vt100`

####Disable Bootup Info
When the Raspberry Pi boots up, all the bootup information is sent to the serial port.
Disabling this bootup information is optional and you may want to leave this enabled
as it is sometimes useful to see what is happening at bootup. If you have a device
connected (i.e. Arduino) at bootup, it will receive this information over the serial port,
so it is up to you to decide whether this is a problem or not. 

You can disable it by editing the file /boot/cmdline.txt
The contents of the file look like this:

`dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1
root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait`

Remove all references to ttyAMA0 (which is the name of the serial port). The file will now look like this:

`dwc_otg.lpm_enable=0 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait`

####Reboot
In order you enable the changes you have made, you will need to reboot the Raspberry Pi:
`sudo shutdown -r now`