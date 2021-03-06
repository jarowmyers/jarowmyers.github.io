# Lab 3 Documentation

This documentation covers the process of setting up an Ubuntu VM through Digital Ocean, installing Docker on it, and installing WireGuard through Docker.

### Create a Digital Ocean Droplet
An account was created on [Digital Ocean](https://www.digitalocean.com/).

On Digital Ocean, a new virtual machine droplet was created. The settings selected for this VM are:

-Ubuntu 20.04 (LTS) x64

-Basic shared CPU

-Regular Intel with SSD

-$5/mo

-San Francisco 3 region


The droplet was given a root password and the identifier 'ubuntu-lab3'.

### Install Docker
The VM console was accessed using the Digital Ocean console window. 

Docker was installed using [this guide](https://thematrix.dev/install-docker-and-docker-compose-on-ubuntu-20-04/
). All steps taken from the guide are documented below, it is just for reference.

Install Docker with the following sequence of commands:
```markdown
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```
```markdown
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```markdown
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
```markdown
apt-cache policy docker-ce
```
```markdown
sudo apt install docker-ce -y
```

Install Docker-Compose with:
```markdown
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
Set permissions:
```markdown
sudo chmod +x /usr/local/bin/docker-compose
```

At this point, the VM will have Docker and Docker-Compose, and these can be used to install WireGuard.

### Install WireGuard

To install WireGuard, the following commands were run:
```markdown
mkdir -p ~/wireguard/
mkdir -p ~/wireguard/config/
nano ~/wireguard/docker-compose.yml
```
The docker-compose.yml file was filled with this content:
```markdown
version: '3.8'
services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=137.184.7.139
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
```
Then start WireGuard with docker-compose:
```markdown
cd ~/wireguard/
docker-compose up -d
```
At this point, the WireGuard server should be running, and it can be used to connect devices to a VPN.

### Using the VPN
The WireGuard application was installed on both a mobile device and a laptop through their respective app stores.
With the app installed, it can be used to connect to the VPN.

To get the QR code for connecting a phone, run the command:
```markdown
docker-compose logs -f wireguard
```
Then, scan the code to connect. Images of the phone IP before and after connecting are shown below, as well as the active tunnel in WireGuard.

The mobile phone IP when not connected.

![](/images/bareIP.PNG)

The IP when connected to the VPN.

![](/images/vpnIP.PNG)

The active tunnel shown in WireGuard.

![](/images/vpnSettings.PNG)

To setup the VPN on a laptop, the contents of the ~/wireguard/config/peer_pc1/peer_pc1.conf file are needed.

In the WireGuard application, the 'Add Empty Tunnel...' option was chosen, and then the contents of the peer_pc1.conf file were copied into the application.

The tunnel was then saved and the device can connect to the VPN.

An image of the laptop IP before and after connecting as well as the active tunnel in WireGuard are shown below.

The unconnected IP, the IP when connected, and the active tunnel in WireGuard.
![laptop images](/images/vpnLaptop.png)
