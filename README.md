# How to Build a SBC Server

This guide goes through all the steps necessary to get a SBC server up and running for Openponics Sensor Network deployment in the field. 

##Install Fresh Image on SBC

First, get a SBC (Raspberry Pi, Banana Pi, etc) up and running with a fresh image. Configure all settings such as

* Time zone
* Keyboard layout 
* SSH Enable
* Change Hostname
* Change password

**Note:** Raspi-config password change does not work on Banana Pi. Instead use:

```sudo passwd bananapi```


##Boot Banana Pi from SSD

These instructions are a derived from these two sites:

[http://banoffeepiserver.com/setup-raspbian-on-a-sata-hard-disk.html](http://banoffeepiserver.com/setup-raspbian-on-a-sata-hard-disk.html)
[http://www.htpcguides.com/move-linux-banana-pi-sata-setup/](http://www.htpcguides.com/move-linux-banana-pi-sata-setup/)
[http://www.yolinux.com/TUTORIALS/LinuxTutorialAdditionalHardDrive.html](http://www.yolinux.com/TUTORIALS/LinuxTutorialAdditionalHardDrive.html)

Commands to list attached mass storage devices and file systems 

```
sudo fdisk -l
sudo mount -l
df -h
```

[Linux USB Drive Command Cheat Sheet](https://www.raspberrypi.org/forums/viewtopic.php?t=38429)


##Install Node.js
Download a compatable version of node.js. **The latest version is not compatible with the current version of the particle sever.**

####All versions of node [can be found here](https://nodejs.org/dist/).

For our setup, we need a version in the 0.10.X range. v0.10.40 has been proven. 

[http://nodejs.org/dist/v0.10.40/node-v0.10.40.tar.gz](http://nodejs.org/dist/v0.10.40/node-v0.10.40.tar.gz)

Can be downloaded on PC and then transfered over with Cyberduck or other SFTP service. 

**DO NOT EXTRACT BEFORE TRANSFERRING**
 
Once SFTPed over, extract with 

```tar -xvf node-v0.10.40.tar.gz```

Change permissions on newly extracted folder

```sudo chown bananapi:pi node-v0.10.40/ -R``` 

Delete .tar and cd into node folder

```
cd node-v0.10.40
./configure --without-snapshot
make -j2
sudo make install
npm cache clean
sudo npm cache clean
```

###Install Node-Red

```sudo npm install -g node-red --unsafe-perm```

###Installing Particle CLI
```
sudo npm install -g particle-cli --unsafe-perm
```
May need to do this double install if it doesn't work the first time. 

```
sudo npm install -g serialport@1.5.0 --unsafe-perm
sudo npm install -g serialport@1.5.0 particle-cli --unsafe-perm
```
[Community Instructions for Installing on RasPi2](https://community.particle.io/t/installing-particle-cli-spark-server-on-raspberry-pi-2/12996)




###Installing Particle Server

```
git clone https://github.com/spark/spark-server.git
cd spark-server
npm install
node main.js
```

[Installing node.js on Raspberry Pi](https://learn.adafruit.com/node-embedded-development/)

[https://github.com/spark/spark-server/blob/master/README.md](https://github.com/spark/spark-server/blob/master/README.md)

[https://github.com/spark/spark-server](https://github.com/spark/spark-server)



[Community Local Cloud 1st Time Instructions](https://community.particle.io/t/tutorial-local-cloud-1st-time-instructions-01-oct-15/5589)

###Starting up Pi Server
```
login as root or other user
screen
node-red (start node-red server) (port 2525(get data from Photon & 1880(web interface))
ctl+a  c (new screen)
cd /opt/spark-server/
node main (start spark server)
```
###Setting up new PC to talk to the Pi Server

```
particle config list
particle config local apiUrl http://192.168.1.141:8080  (IP Address of Pi Server)
particle config local
particle login
particle list
```



###Adding new Photon to my network (Cloud Switching (Local vs Particle))
```
Plug Photon into Pi 
Setup Photon on local WiFi
particle setup wifi (flashing blue)
particle identify 
copy DEVICE_ID
Put in DFU Mode
cd /opt/spark-server
particle keys server default_key.pub.pem 192.168.1.141  (pi address)
cd /core_keys
particle keys save (photon ID)
delete all other crap
reset photon
particle device add your_ID
particle device rename DEVICE_ID New_Name
particle list

```

###Adding new Photon to my network (Cloud Switching (Local vs Particle))

* Plug Photon into computer 
* Download [this file](https://s3.amazonaws.com/spark-website/cloud_public.der) - may need to rename from .cer to .der
* `cd` to the directory that file lives in
* Place your Core in DFU-mode (flashing yellow)
* On the command line (to switch to Particle Cloud):
```
particle keys server cloud_public.der
```
* Manually reset Photon 
* Switch clouds: 
```
particle config list
particle config identify
particle config particle
particle login
particle list
```
* Should see newly added Photon in list 



###Send code to Photon on my own network
* compile and download .bin file from Particle Build
* particle flash nameOfPhoton /your/location/firmware.bin

###Setting up WiFi through CLI
```
sudo iwlist wlan0 scan |less
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
ifdown wlan0
ifup wlan0
ifconfig
```
###Starting Node Red and Spark Server Automatically

First, we need to install Forever
```npm install -g forever```

We started by trying to get Crontab to run both node-red and the spark server. It worked for the node red server but it would not work for the spark server. To edit crontab, type:
```crontab -u root -e```
at the bottom of the file, type:

```
@reboot forever start /usr/local/lib/node_modules/node-red/red.js --settings /root/.node-red/settings.js
#@reboot forever start /opt/spark-server/main.js
```
the bottom line is the one that failed to run correctly

From there, we attmepted to use [Forever-Service](https://github.com/zapty/forever-service) to run both. 

```
npm install -g forever-service
cd /opt/spark-server
forever-service install sparkserver --script main.js
cd /etc/init.d
service sparkserver status
service sparkserver start

To see if it actually worked, do:
cd /var/log/
tail -f sparkserver.log

forever list
```

As it turned out, the spark-server would start this way, but the Node-red server would not. Thus, I used crontab to start the node-red server and forevr-service to keep the spark-server running even after reboot. 

to kill the forever-service, type:

###Node Red Web interface
http://192.168.1.141:1880/  (Pi IP + :1880)

###Dashboard
http://192.168.1.141:1880/dash  


###Updating Photon Device Firmware Over USB
* download latest version from https://github.com/spark/firmware/releases
* move to directory where files where downloaded

Photon:
```
dfu-util -d 2b04:d006 -a 0 -s 0x8020000 -D system-part1-0.4.7-photon.bin
dfu-util -d 2b04:d006 -a 0 -s 0x8060000:leave -D system-part2-0.4.7-photon.bin
```
P1:
````
dfu-util -d 2b04:d008 -a 0 -s 0x8020000 -D system-part1-0.4.7-p1.bin
dfu-util -d 2b04:d008 -a 0 -s 0x8060000:leave -D system-part2-0.4.7-p1.bin
```


