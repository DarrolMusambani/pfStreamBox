# pfStreamBox

Set up a Raspberry Pi to stream your Pen&Paper-Sessions to distant Players.


## Hardware
- Raspberry Pi 4, 4GB
- Raspi-Cam V2
- USB-Microphone

## Software
- OS: Raspian Buster
- Node-Red
  - node-red-dashboard
- Chromium
- unclutter
- dnsmasq
- hostapd

# Installation
Get a fresh "Raspbian Buster with desktop"-image from [RaspberryPi.org](https://www.raspberrypi.org/downloads/raspbian/) and flash it to your sd-card.
Put it into your RaspberryPi, boot up and connect it to the internet. Also change the default-password.

Now man yourself up and run console commands as root:
`sudo su`

Update your RaspberryPi:
```
apt-get update
apt-get upgrade
```

Install Node-RED using the provided install-script from [nodered.org](https://nodered.org/docs/getting-started/raspberrypi). This must be done as pi so type `exit` if you're currently runnig as root.

`bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)`

Install the package *node-red-dashboard* via `Menu - Manage palette` in Node-RED.

Install additional software:

`apt-get install -y unclutter dnsmasq hostapd`

Then stop and deactivate *dnsmasq* and *hostapd* services:

```
systemctl stop dnsmasq
systemctl stop hostapd
systemctl disable dnsmasq
systemctl disable hostapd
```
They will later only be activated if there is no known WiFi-AP nearby.

Since Chromium is the standardbrowser in Raspian Buster it doesn't have to be installed...

# Configuration

## RaspberryPi
To make this running, there are only a few modifications to make in Raspian
### System settings
Use `raspi-config` in console or the graphical counterpart for the following settings:
- System
  - set the hostname as you wish
  - boot to desktop
  - automatically login as "pi"
  - DO NOT wait for network at boot
- Interfaces
  - activate camera
- Performance
  - make sure your GPU can breathe by giving it 128 or more MB RAM
- Localisation
  - set your locales as you need it

### Configure WiFi-AP



### Configure LXDE/Chromium to autostart in kiosk-mode

## Node-Red
