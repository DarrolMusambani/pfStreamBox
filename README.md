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

Nodes to scan for WiFi-AP's and adding a new Accespoint to wpa_supplicant.conf:
```
[{"id":"68f99ff1.5e5ec","type":"tab","label":"Flow 2","disabled":false,"info":""},{"id":"a446da9a.ef8b88","type":"exec","z":"68f99ff1.5e5ec","command":"","addpay":true,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":390,"y":180,"wires":[["59451f61.22c178"],[],[]]},{"id":"da22310c.7cba4","type":"ui_button","z":"68f99ff1.5e5ec","name":"","group":"63927d90.1d26e4","order":4,"width":0,"height":0,"passthru":false,"label":"WiFi-Scan","tooltip":"Nach WLAN-Netzen suchen","color":"","bgcolor":"","icon":"","payload":"iwlist wlan0 scan | awk -F ':' '/ESSID:/ {print $2;}'","payloadType":"str","topic":"","x":150,"y":200,"wires":[["a446da9a.ef8b88"]]},{"id":"a83c4eaf.efbd48","type":"ui_dropdown","z":"68f99ff1.5e5ec","name":"Verfügbare Netze","label":"","tooltip":"Neues WLAN auswählen","place":"Neues WLAN auswählen","group":"63927d90.1d26e4","order":5,"width":0,"height":0,"passthru":false,"options":[{"label":"Keine Auswahl","value":"None","type":"str"}],"payload":"","topic":"essid","x":370,"y":320,"wires":[["35ca91d2.8cb4d6","b6f14088.e58a"]]},{"id":"59451f61.22c178","type":"function","z":"68f99ff1.5e5ec","name":"AP-Liste erstellen","func":"var input = msg.payload;\n//input = input.replace(/(\\\"\\\")/gm,'');\ninput = input.replace(/\\\"(\\r\\n|\\n|\\r)/gm,',');\ninput = input.replace(/(\\\")/gm,'');\nvar output = input.split(\",\");\noutput.push(\"Neu verbinden\");\nmsg.payload = output.filter(Boolean);\nreturn msg;","outputs":1,"noerr":0,"x":590,"y":180,"wires":[["b3ed998.21b54e8"]]},{"id":"b3ed998.21b54e8","type":"change","z":"68f99ff1.5e5ec","name":"","rules":[{"t":"set","p":"options","pt":"msg","to":"payload","tot":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":170,"y":320,"wires":[["a83c4eaf.efbd48"]]},{"id":"60781ec5.f365b","type":"ui_text_input","z":"68f99ff1.5e5ec","name":"","label":"WLAN Passwort","tooltip":"","group":"63927d90.1d26e4","order":6,"width":0,"height":0,"passthru":false,"mode":"text","delay":"500","topic":"wifiKey","x":860,"y":400,"wires":[["b6f14088.e58a"]]},{"id":"8921da21.04f858","type":"ui_button","z":"68f99ff1.5e5ec","name":"Neue Verbindung hinzu","group":"63927d90.1d26e4","order":8,"width":0,"height":0,"passthru":false,"label":"Verbindung einrichten","tooltip":"Neue Verbindung einrichten","color":"","bgcolor":"","icon":"fa-plug","payload":"true","payloadType":"bool","topic":"connectNow","x":890,"y":460,"wires":[["b6f14088.e58a"]]},{"id":"cd9d0b90.cede7","type":"inject","z":"68f99ff1.5e5ec","name":"","topic":"","payload":"","payloadType":"str","repeat":"","crontab":"","once":true,"onceDelay":0.1,"x":140,"y":400,"wires":[["4eccf2a9.61cfac"]]},{"id":"4eccf2a9.61cfac","type":"function","z":"68f99ff1.5e5ec","name":"UI-Elemente deaktivieren","func":"msg.payload = \"?\" ;\nmsg.enabled = false ;\nreturn msg;","outputs":1,"noerr":0,"x":360,"y":400,"wires":[["8921da21.04f858","60781ec5.f365b"]]},{"id":"35ca91d2.8cb4d6","type":"function","z":"68f99ff1.5e5ec","name":"UI-Elemente reaktivieren","func":"msg.enabled = true ;\nreturn msg;","outputs":1,"noerr":0,"x":610,"y":320,"wires":[["8921da21.04f858","1e442a03.68caee"]]},{"id":"1e442a03.68caee","type":"change","z":"68f99ff1.5e5ec","name":"","rules":[{"t":"delete","p":"payload","pt":"msg"}],"action":"","property":"","from":"","to":"","reg":false,"x":880,"y":300,"wires":[["60781ec5.f365b"]]},{"id":"f3ea8157.d22d8","type":"comment","z":"68f99ff1.5e5ec","name":"Verbindung ändern","info":"","x":150,"y":160,"wires":[]},{"id":"5ca567d4.05c53","type":"inject","z":"68f99ff1.5e5ec","name":"Scan starten","topic":"","payload":"iwlist wlan0 scan | awk -F ':' '/ESSID:/ {print $2;}'","payloadType":"str","repeat":"","crontab":"","once":true,"onceDelay":"0.2","x":150,"y":260,"wires":[["a446da9a.ef8b88"]]},{"id":"b6f14088.e58a","type":"function","z":"68f99ff1.5e5ec","name":"Collect Input and create CLI-command","func":"context.data = context.data || new Object();\n\nswitch (msg.topic) {\n    case \"essid\":\n        context.data.essid = msg.payload;\n        msg = null;\n        break;\n    case \"wifiKey\":\n        context.data.wifiKey = msg.payload;\n        msg = null;\n        break;\n    case \"connectNow\":\n        context.data.connectNow = msg.payload;\n        msg = null;\n        break;\n        \n    default:\n        msg = null;\n    \tbreak;\n\n}\n\nif(context.data.essid != null && context.data.wifiKey != null && context.data.connectNow == true) {\n\tcontext.data.payload = `wpa_passphrase ${context.data.essid} ${context.data.wifiKey} | sudo tee -a /etc/wpa_supplicant/wpa_supplicant.conf > /dev/null | wpa_cli -i wlan0 reconfigure | sudo shutdown -r -t 1`;\n\tmsg = new Object();\n\tmsg = context.data;\n\t\n\tcontext.data=null;\n\treturn msg;\n} else return msg;","outputs":1,"noerr":0,"x":1190,"y":360,"wires":[["55b0305.969325"]]},{"id":"55b0305.969325","type":"exec","z":"68f99ff1.5e5ec","command":"","addpay":true,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"generische Befehle","x":1170,"y":540,"wires":[[],[],[]]},{"id":"63927d90.1d26e4","type":"ui_group","z":"","name":"WLAN Konfiguration","tab":"d08f9166.a137","order":1,"disp":true,"width":"10","collapse":false},{"id":"d08f9166.a137","type":"ui_tab","z":"","name":"StreamBox - Konfiguration","icon":"fa-wifi","order":1,"disabled":false,"hidden":false}]
```
![Scan and connect AP's](https://github.com/DarrolMusambani/pfStreamBox/blob/master/images/nodes_wifi_add.PNG)
### Setup Audio/Video controls
Raspbian Buster has v4l-utils included so we can use v4l2-ctl to configure some camera-settings while streaming.

Nodes to control contrast/brightness/saturation/sharpness as well as image rotation:
```
[{"id":"68f99ff1.5e5ec","type":"tab","label":"Flow 2","disabled":false,"info":""},{"id":"f5a0fc0d.2f67a","type":"ui_slider","z":"68f99ff1.5e5ec","name":"","label":"Kontrast","tooltip":"","group":"8cd16966.f464d8","order":1,"width":0,"height":0,"passthru":false,"outs":"end","topic":"contrast","min":"-100","max":"100","step":1,"x":740,"y":140,"wires":[["c7592539.eb2d9"]]},{"id":"ed715a2f.f343","type":"exec","z":"68f99ff1.5e5ec","command":"","addpay":true,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"generische Befehle","x":1210,"y":380,"wires":[[],[],[]]},{"id":"fc98c64.2288db8","type":"exec","z":"68f99ff1.5e5ec","command":"v4l2-ctl -C contrast | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":140,"wires":[["f5a0fc0d.2f67a"],[],[]]},{"id":"9cae17f.0530c68","type":"inject","z":"68f99ff1.5e5ec","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":true,"onceDelay":0.1,"x":130,"y":260,"wires":[["fc98c64.2288db8","16885e26.c565ba","9746add3.31d7f8","a3dc58a3.d0c5c8"]]},{"id":"c7592539.eb2d9","type":"function","z":"68f99ff1.5e5ec","name":"CLI-Befehl aufbauen","func":"var sliderValue = msg.payload;\nvar parameterName = msg.topic;\nvar cliCommand = `v4l2-ctl -c ${parameterName}=${sliderValue}`;\nmsg.payload = cliCommand;\nreturn msg;","outputs":1,"noerr":0,"x":960,"y":260,"wires":[["ed715a2f.f343"]]},{"id":"7eb25553.9cdab4","type":"ui_slider","z":"68f99ff1.5e5ec","name":"","label":"Helligkeit","tooltip":"","group":"8cd16966.f464d8","order":2,"width":0,"height":0,"passthru":false,"outs":"end","topic":"brightness","min":"-100","max":"100","step":1,"x":740,"y":220,"wires":[["c7592539.eb2d9"]]},{"id":"16885e26.c565ba","type":"exec","z":"68f99ff1.5e5ec","command":"v4l2-ctl -C brightness | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":220,"wires":[["7eb25553.9cdab4"],[],[]]},{"id":"bb10c313.39414","type":"ui_slider","z":"68f99ff1.5e5ec","name":"","label":"Sättigung","tooltip":"","group":"8cd16966.f464d8","order":3,"width":0,"height":0,"passthru":false,"outs":"end","topic":"saturation","min":"-100","max":"100","step":1,"x":740,"y":300,"wires":[["c7592539.eb2d9"]]},{"id":"9746add3.31d7f8","type":"exec","z":"68f99ff1.5e5ec","command":"v4l2-ctl -C saturation | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":300,"wires":[["bb10c313.39414"],[],[]]},{"id":"632bf710.86e6c","type":"ui_slider","z":"68f99ff1.5e5ec","name":"","label":"Schärfe","tooltip":"","group":"8cd16966.f464d8","order":4,"width":0,"height":0,"passthru":false,"outs":"end","topic":"sharpness","min":"-100","max":"100","step":1,"x":740,"y":380,"wires":[["c7592539.eb2d9"]]},{"id":"a3dc58a3.d0c5c8","type":"exec","z":"68f99ff1.5e5ec","command":"v4l2-ctl -C sharpness | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":440,"y":380,"wires":[["632bf710.86e6c"],[],[]]},{"id":"2c3fb277.f1db26","type":"ui_button","z":"68f99ff1.5e5ec","name":"Gegen den Uhrzeigersinn drehen","group":"8cd16966.f464d8","order":9,"width":"3","height":"1","passthru":false,"label":"","tooltip":"Gegen den Uhrzeigersinn drehen","color":"","bgcolor":"","icon":"fa-undo","payload":"-90","payloadType":"num","topic":"Im Uhrzeigersinn drehen","x":220,"y":600,"wires":[["164aed48.71b85b","e6b1bfe5.67ec18"]]},{"id":"1773c7f6.19a86","type":"ui_button","z":"68f99ff1.5e5ec","name":"Im Uhrzeigersinn drehen","group":"8cd16966.f464d8","order":10,"width":"3","height":"1","passthru":false,"label":"","tooltip":"Im Uhrzeigersinn drehen","color":"","bgcolor":"","icon":"fa-repeat","payload":"90","payloadType":"num","topic":"rotate","x":190,"y":480,"wires":[["164aed48.71b85b","e6b1bfe5.67ec18"]]},{"id":"164aed48.71b85b","type":"exec","z":"68f99ff1.5e5ec","command":"v4l2-ctl -C rotate | grep -Eo '[0-9]{1,4}'","addpay":false,"append":"","useSpawn":"false","timer":"","oldrc":false,"name":"","x":230,"y":540,"wires":[["33e708f4.d6598"],[],[]]},{"id":"e6b1bfe5.67ec18","type":"join","z":"68f99ff1.5e5ec","name":"","mode":"custom","build":"array","property":"payload","propertyType":"msg","key":"topic","joiner":"\\n","joinerType":"str","accumulate":false,"timeout":"","count":"2","reduceRight":false,"reduceExp":"","reduceInit":"","reduceInitType":"","reduceFixup":"","x":770,"y":540,"wires":[["de159571.668438"]]},{"id":"33e708f4.d6598","type":"function","z":"68f99ff1.5e5ec","name":"String to Number","func":"function isNumeric(n) {\n  return !isNaN(parseFloat(n)) && isFinite(n);\n}\n\nif (isNumeric(msg.payload)){\n msg.payloadString = msg.payload.concat(\"ms\");   \n msg.payload = Number(msg.payload);\n}\nreturn msg;","outputs":1,"noerr":0,"x":490,"y":540,"wires":[["e6b1bfe5.67ec18"]]},{"id":"de159571.668438","type":"function","z":"68f99ff1.5e5ec","name":"CLI-Befehl aufbauen","func":"var rotationChange = msg.payload[0];\nvar currentRotation = msg.payload[1];\n\nif (currentRotation == 0){\n    currentRotation = 360;\n}\n\nvar newRotation = currentRotation + rotationChange;\nif (newRotation >= 360){\n    newRotation = newRotation - 360;\n}\nmsg.payload = `v4l2-ctl -c rotate=${newRotation}`;\nreturn msg;","outputs":1,"noerr":0,"x":950,"y":500,"wires":[["ed715a2f.f343"]]},{"id":"725c7a20.6b0c5c","type":"ui_text","z":"68f99ff1.5e5ec","group":"8cd16966.f464d8","order":8,"width":0,"height":0,"name":"","label":"Bildrotation","format":"{{msg.payload}}","layout":"col-center","x":160,"y":440,"wires":[]},{"id":"8cd16966.f464d8","type":"ui_group","z":"","name":"Videoeinstellungen","tab":"d08f9166.a137","order":2,"disp":true,"width":"6","collapse":false},{"id":"d08f9166.a137","type":"ui_tab","z":"","name":"StreamBox - Konfiguration","icon":"fa-wifi","order":1,"disabled":false,"hidden":false}]
```
![Videocontrol](https://github.com/DarrolMusambani/pfStreamBox/blob/master/images/nodes_videocontrol.PNG)
