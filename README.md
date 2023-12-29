# projectpi
An attempt to install OpenProject on Raspberry Pi 4

# Image your Pi
I used Raspberry Pi Imager v1.8.4 from https://www.raspberrypi.com/software/
  Choose Raspberry Pi 4
  For Operating System choose Ubuntu Server 22.04.3 LTS (64-Bit) 
  I used the settings dialog to configure my user account, wifi, and SSH access in advanced options.
  When complete eject the disk, insert it into your Pi and boot up.
  For first boot I had a screen connected so I could find my IP for SSH access

# First Boot
  Power on and wait for the system to go through it's initial configuration. Login and update. Then determine your IP address and reboot for good measure.
  ## Login
  Using the credentials you set in the Pi Imager. If you setup SSH Keys it will display the host key and pause so you'll have to hit enter prior to login
```
ip addr
```
  Make note of the ip for ssh access 
  ## Reboot
```
sudo reboot
```
# Establish Access & Update the OS
  ## SSH Access
    From Powershell on the machine you used to image the disk you can easily connect.
```
ssh USERNAME@IPADDRESS
```
    on first connect, it will ask to accept the fingerprint, if you do, type "yes". It will add the host and you'll be logged in.
  ## Update Pi
```
sudo apt-get update
```
NOTE that I had to reboot between update and upgrade on my installation as a file was locked. I am not too familiar with how much you can do in Linux without a reboot so I tend to reboot obsessively in between "large" actions.
```
sudo reboot
```
reconnect to ssh
```
sudo apt-get upgrade -y
```
after the updgrade it will prompt to restart services. you can accept the default selections and tab to select "OK". on the first upgrade you may find that it will flash a Kernel update and you will need to reboot again after anyway. 
```
sudo reboot
```
reconnect to ssh
# Install Docker Engine
I used the instructions from https://docs.docker.com/engine/install/ubuntu/ and most of this is copied verbatim from there
  
