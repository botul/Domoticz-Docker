## Domoticz-Docker
docker-compose Domoticz configuration with mqtt broker and zigbee2mqtt containers on Raspberry Pi (4)

Domoticz is a Home Automation System that lets you monitor and configure various devices like: lights, switches, various sensors/meters like temperature, humidity, electra, gas, water and much more. Notifications/Alerts can be sent to any mobile device.
Below you will find a small, step-by-step setup environment with domoticz, mqtt and zigbee2mqtt configuration in one docker-compose file.

## Hardware that I used: 
* Raspberry Pi 4 with 2GB RAM.
* CC2531 USB dongle (zigbee)

## Configure fresh Raspbian Linux installation:

#### Start and enable ssh server:
`sudo systemctl start ssh`
`sudo systemctl enable ssh`

#### check IP, connect remotely
`ifconfig`

#### start and enable ssh server
`sudo systemctl start ssh`
`sudo systemctl enable ssh`

`$sudo raspi-config`

Setup:
* hostname
* timezone

* reboot

#### Now you can log in to your Raspberry remotely (I preffer this way of work with linux servers)
`$ssh -l pi XXX.XXX.X.XXX`

#### System upgrade (after fresh installation it can took few minutes...)
`$sudo apt update`
`$sudo apt upgrade`

#### Set up static IP
`sudo nano /etc/dhcpcd.conf`

```
interface eth0
static ip_address=192.168.1.10/24
static ip6_address=fd51:42f8:caae:d92e::ff/64
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 1.1.1.1 fd51:42f8:caae:d92e::1
```
#### Enable ipv4 forward
`sudo sysctl -w net.ipv4.ip_forward=1`


#### Install additional packages (Python, pip. git):
`sudo apt install git python3-requests python3-pip`

### Setup Python (set python3 as default)
These two commands set priorities for different versions of Python installed in Raspbian. This is the best way to configure it, do not try to change names in `/usr/bin/` as you can find in some documents in Internet!
Higher priority ('2' in this case) is default.

`$update-alternatives --install /usr/bin/python python /usr/bin/python3 2`

`$update-alternatives --install /usr/bin/python python /usr/bin/python2 1`

You can switch between Python versions using below command:
`$sudo update-alternatives --config python`

### Docker installation:

`$curl -fsSL https://get.docker.com -o get-docker.sh`

Once the download is complete, execute the script by typing:
`sh get-docker.sh `

The script will detect the Linux distribution, install the required packages, and start Docker.

Docker compose installation:
`$sudo apt install docker-compose`

On my raspberry I have configured my own user account (I am not using standart 'pi' account).
If you would like also use your own account, please create it, and join to 'docker' and 'sudo' group (replace 'botul' with your username):

`$usermod -aG docker botul`

`$usermod -aG sudo botul`

If you would like to use 'sudo' command without entering password every time, please configure visudo.
Be aware to secure your account with strong password and setup ssh key authentication!

### Checking '/dev/...' location of CC2531 USB dongle:  
`$sudo ls /dev | grep ACM`
result should looks like: `ttyACM0` - in this case device path is `/dev/ttyACM0`

### Creating shared folder for docker containers:
`$mkdir -p ./docker-data/domoticz/`

`$mkdir -p ./docker-data/mqtt/`

`$mkdir -p ./docker-data/zigbee/`


### Creating and configuring docker images:

** Domoticz
(Please change paths to your home directory)

```
$docker create \
--name=domoticz \
-e PUID=1001 \
-e PGID=1001 \
-e TZ=Europe/Warsaw \
-p 8080:8080 \
-p 6144:6144 \
-p 1443:1443 \
-v /home/botul/docker-data/domoticz:/config \
--device /dev/ttyACM0:/dev/ttyACM0 \
--restart unless-stopped \
linuxserver/domoticz:latest
```
The same in one line:

`docker create --name=domoticz -e PUID=1001 -e PGID=1001 -e TZ=Europe/Warsaw -p 8080:8080 -p 6144:6144 -p 1443:1443 -v /home/botul/docker-data/domoticz:/config --device /dev/ttyACM0:/dev/ttyACM0 --restart unless-stopped linuxserver/domoticz:latest`

