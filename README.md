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

### Setup WiFi controls

### Setup Audio/Video controls
Raspbian Buster has v4l-utils included so we can use v4l2-ctl to configure some camera-settings while streaming.

Nodes to control contrast/brightness/saturation/sharpness:
```
[{"id":"765754f6.2f6d8c","type":"ui_slider","z":"ebb0d3c0.e961","name":"","label":"Kontrast","tooltip":"","group":"8cd16966.f464d8","order":1,"width":0,"height":0,"passthru":false,"outs":"end","topic":"contrast","min":"-100","max":"100","step":1,"x":740,"y":700,"wires":[["b6af6cec.be35a"]]},{"id":"8b40e436.552598","type":"exec","z":"ebb0d3c0.e961","command":"","addpay":true,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"generische Befehle","x":1310,"y":880,"wires":[[],[],[]]},{"id":"ebd8674.963ed98","type":"exec","z":"ebb0d3c0.e961","command":"v4l2-ctl -C contrast | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":700,"wires":[["765754f6.2f6d8c"],[],[]]},{"id":"d3d30f2e.f661e","type":"inject","z":"ebb0d3c0.e961","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":true,"onceDelay":0.1,"x":130,"y":820,"wires":[["ebd8674.963ed98","a53b88b0.41b498","d5be04f9.ccbed8","e8499381.eef92","e3f546a.86b03b8","e4da1afe.784fc8"]]},{"id":"b6af6cec.be35a","type":"function","z":"ebb0d3c0.e961","name":"CLI-Befehl aufbauen","func":"var sliderValue = msg.payload;\nvar parameterName = msg.topic;\nvar cliCommand = `v4l2-ctl -c ${parameterName}=${sliderValue}`;\nmsg.payload = cliCommand;\nreturn msg;","outputs":1,"noerr":0,"x":1020,"y":900,"wires":[["8b40e436.552598"]]},{"id":"e105b453.5a8238","type":"ui_slider","z":"ebb0d3c0.e961","name":"","label":"Helligkeit","tooltip":"","group":"8cd16966.f464d8","order":2,"width":0,"height":0,"passthru":false,"outs":"end","topic":"brightness","min":"-100","max":"100","step":1,"x":740,"y":780,"wires":[["b6af6cec.be35a"]]},{"id":"a53b88b0.41b498","type":"exec","z":"ebb0d3c0.e961","command":"v4l2-ctl -C brightness | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":780,"wires":[["e105b453.5a8238"],[],[]]},{"id":"c54e691.c99bc98","type":"ui_slider","z":"ebb0d3c0.e961","name":"","label":"Sättigung","tooltip":"","group":"8cd16966.f464d8","order":3,"width":0,"height":0,"passthru":false,"outs":"end","topic":"saturation","min":"-100","max":"100","step":1,"x":740,"y":860,"wires":[["b6af6cec.be35a"]]},{"id":"d5be04f9.ccbed8","type":"exec","z":"ebb0d3c0.e961","command":"v4l2-ctl -C saturation | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":860,"wires":[["c54e691.c99bc98"],[],[]]},{"id":"fd1c2249.f03da","type":"ui_slider","z":"ebb0d3c0.e961","name":"","label":"Schärfe","tooltip":"","group":"8cd16966.f464d8","order":4,"width":0,"height":0,"passthru":false,"outs":"end","topic":"sharpness","min":"-100","max":"100","step":1,"x":740,"y":940,"wires":[["b6af6cec.be35a"]]},{"id":"e8499381.eef92","type":"exec","z":"ebb0d3c0.e961","command":"v4l2-ctl -C sharpness | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":940,"wires":[["fd1c2249.f03da"],[],[]]},{"id":"8cd16966.f464d8","type":"ui_group","z":"","name":"Videoeinstellungen","tab":"d08f9166.a137","order":2,"disp":true,"width":"6","collapse":false},{"id":"d08f9166.a137","type":"ui_tab","z":"","name":"StreamBox - Konfiguration","icon":"fa-wifi","order":1,"disabled":false,"hidden":false}]
```
