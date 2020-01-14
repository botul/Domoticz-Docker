# Domoticz-Docker
Domoticz configuration in Docker containers on Raspberry Pi (4) with mqtt broker and zigbee2mqtt


Start and enable ssh server:

sudo systemctl start ssh
sudo systemctl enable ssh

#check IP, connect remotely
ifconfig

#start and enable ssh server
sudo systemctl start ssh
sudo systemctl enable ssh

$ssh -l pi XXX.XXX.X.XXX

$sudo apt update
$sudo apt upgrade

$sudo raspi-config
Setup:
- hostname
- timezone
reboot
