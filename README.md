# projectpi
An attempt to install OpenProject on Raspberry Pi 4.
This took me about an hour including writing (mostly copy/pasting) the how to below, but I am pretty familiar with the Pi and Linux command line so it may take you a little longer.

# Image your Pi
I used Raspberry Pi Imager v1.8.4 from https://www.raspberrypi.com/software/ <br>
  Choose Raspberry Pi 4 <br>
  For Operating System choose Ubuntu Server 22.04.3 LTS (64-Bit) <br>
  I used the settings dialog to configure my user account, wifi, and SSH access in advanced options.<br>
  When complete eject the disk, insert it into your Pi and boot up.<br>
  For first boot I had a screen connected so I could find my IP for SSH access<br>

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
I used the instructions from https://docs.docker.com/engine/install/ubuntu/ and most of this is copied verbatim from there<br>
  first confirm there are no conflicting installations and if so uninstall them
  ```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
## Install the Repos
for me these all were already installed, but I ran the code anyway and it set them to manually installed
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
## Install the packages
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
you will have to type "y" or hit enter to accept. you can also always append "-y" to the command to preemptively accept
## Test Docker
```
sudo docker run hello-world
```
on my installation it returned the following <br>
<pre>
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
478afc919002: Pull complete
Digest: sha256:ac69084025c660510933cca701f615283cdbb3aa0963188770b54c31c8962493
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
</pre><br>
## Upgarde (in the future)
Repeat the install step
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
# Install OpenProject
I used the instructions from https://www.openproject.org/docs/installation-and-operations/installation/docker/ and most of this is copied verbatim from there<br>
I used the recommended "one container per process" method<br>
## Clone the repo
```
git clone https://github.com/opf/openproject-deploy --depth=1 --branch=stable/13 openproject
```
## Run Compose & Setup with Pull
Change to the compose directory for the following commands to run
```
cd openproject/compose
```
Pull the latest. After running the pull command, I was informed that I don't have docker-compose installed so I used snap to install it.
```
sudo apt  install docker-compose -y
```
Now pull the latest (the OpenProject how to does not use Sudo in the tutorial, but I did not have permissions to pull without it)<br>
The pull took a bit, I suppose this is the acutal setup of OpenProject. I didn't keep track, but it was less than 10 minutes.
```
sudo docker-compose pull
```
## Launch
OpenProject has a note about how this command will launch openproject without https on localhost. In production you will want to use https and I would recommend using Certbot from Let's Encrypt and I will update this if and when I do so for myself.<br>
same comment as above about running with sudo permissions
```
sudo OPENPROJECT_HTTPS=false docker-compose up -d
```
## Test
OpenProject does note that "after awhile, OpenProject should be up and running"<br> 
I then navigated to http://RaspPiIPAddress:8080/<br>
I first received nothing, then a 503 error<br>
After about 6 or 7 minutes I refreshed and was welcomed with an OpenProject sign in page<br>
the default login is<br>
***admin***<br>
***admin***<br>
it then prompts for a new password for admin user
# Complete for now
upon logging in it asks to change the hostname as it is not designed to run off of localhost so I will be tackling this in the future
