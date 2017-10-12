
## Fresh Raspbian Install on Raspberyy Pi Zero W (2017)

### Update Pi

```sudo apt-get update && sudo apt-get upgrade```

### Update NodeRed 
(Shouldn't need to do this if you just installed latest version of Rasbian)

```update-nodejs-and-nodered```

### Configure Pi

```sudo raspi-config```

Change timezone, keyboard, hostname, password, enable camera, SPI, I2C, 1Wire, SSH. 

### Configure WiFi Access Point (AP)

Used instructions found here:

[http://imti.co/post/145442415333/raspberry-pi-3-wifi-station-ap](https://github.com/JoelEB/SparkServer/edit/master/SparkServer2017.md)

```sudo nano /etc/network/interfaces```


```
source-directory /etc/network/interfaces.d

auto lo
auto eth0
auto wlan0
auto uap0

iface eth0 inet dhcp
iface lo inet loopback

allow-hotplug wlan0

iface wlan0 inet dhcp
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

iface uap0 inet static
  address 192.168.50.1
  netmask 255.255.255.0
  network 192.168.50.0
  broadcast 192.168.50.255
gateway 192.168.50.1
```

May need to change IP address once this has ben done with more than one Pi. 

* Test with `ifconfig`

```sudo apt-get install hostapd dnsmasq```

```sudo nano /etc/hostapd/hostapd.conf```

```
interface=uap0
ssid=TestAP
hw_mode=g
channel=7
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=badpassword
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```

