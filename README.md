# pfStreamBox

Installationsanweisung und Konfigurationsbackup für einen Jitsi-Client zur Übertragung von Pathfinder-Sessions.


## Hardware
- Raspberry Pi 4, 4GB
- Raspi-Cam V2
- USB-Mikrofon

## Software
- OS: Raspian Buster
- Node-Red
  - node-red-dashboard
- Chromium
- unclutter
- dnsmasq
- hostapd

# Installation
Image "Raspbian Buster with desktop" von RaspberryPi.org beziehen und auf die SD-Karte bringen. SD-Karte einlegen, System starten und mit dem Internet verbinden.
Anschließend das System aktualisieren.

Zusätzliche Software installieren:

`apt-get install -y unclutter dnsmasq hostapd`

Anschließend *dnsmasq* und *hostapd* Services deaktivieren:

`systemctl disable dnsmasq
systemctl disable hostapd`

# Konfiguration

## RaspberryPi

## Node-Red
