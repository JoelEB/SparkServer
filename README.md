# SparkServer
Spark (Particle) Server Instructions

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

###Installing Particle CLI
```
sudo npm install -g particle-cli --unsafe-perm
```
[Community Instructions for Installing on RasPi2](https://community.particle.io/t/installing-particle-cli-spark-server-on-raspberry-pi-2/12996)

###Adding new Photon to my network
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


