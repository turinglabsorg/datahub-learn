https://www.youtube.com/watch?v=89U866LwzBw

Download and install virtual box 
download the Valora app on android/ios
Download debian 10 64 bit
Install debian 10 inside virtual box
Add user (aidan) to sudo group
sudo -s
su - [enter root password]
cat /etc/passwd
cat /etc/group
cat /etc/group | grep aidan
usermod -aG sudo $USER
(log in again)
id  [make sure user is in sudo group now] then... 
 
Update debian 10:”sudo apt update”, “sudo apt upgrade”
Install necessary dependencies
A.docker.io: `sudo apt install docker.io`
B. curl: `sudo apt install curl`
-sudo apt install vim 
C. compiler (needed to build vbox guest additions as well as celocli).  `sudo apt install build-essential linux-headers-`uname-r``
B.open vmware tools: `sudo apt install open-vm-tools-desktop`
C.node.js:
`curl -o- https://raw.githubusercontent.com/nvm...​ | bash`

source ~/.bashrc
-#!/bin/bash
-nvm install 10
-nvm use 10
--npm install -g @celo/celocli
-#!/bin/bash
-set -x
-if test -f celo.env; then
      . celo.env
fi
      -  curl -o celo.env https://gist.githubusercontent.com/al...​ 
-curl -o start_celo.sh https://gist.githubusercontent.com/al...​
curl -o celo.env https://gist.github.com/alchemydc/e28...​
chmod u+x start_celo.sh
pwd
curl -o- https://raw.githubusercontent.com/nvm...​ | bash
curl -o celo.env https://gist.githubusercontent.com/al...​


mkdir celo-data-dir
cd celo-data-dir
mv ../celo.env .
mv ../start_celo.sh .
source celo.env
sudo usermod -aG docker aidan
id 
restart
id
cd celo-data-dir/
source celo.env
echo $CELO_IMAGE
cat celo.env
docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new
copy your account address
vim celo.env
paste in address
exit vim
source celo.env
curl -o start_celo.sh https://gist.githubusercontent.com/al...​
./start_celo.sh
Create shell wrapper for Celo environment: 
https://gist.github.com/alchemydc/ce7...​
Start the docker image (light node) `./start_celo.sh`
watch vid around 1:37​

Celocli commands on full node:
celocli node:synced
celocli account:list
celocli account:balance $ACCOUNT
To send funds back from full node to phone, we need to unlock the key
Start geth console
docker exec -it celo-fullnode geth attach
celocli account:unlock $


Send from node to phone:
celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e18

Node:'0xb7B7cf65Ec11c37858544dc24FF7491eA1715Ca7'

Phone:0x130d1ca2350097777abe31457cf7fefe6cf930c3




export NODE_ADDRESS=”0xb7B7cf65Ec11c37858544dc24FF7491eA1715Ca7”
export PHONE_ADDRESS=”0x130d1ca2350097777abe31457cf7fefe6cf930c3”



aidan@celo:~$ cd /media/cdrom
cdrom/  cdrom0/ 
aidan@celo:~$ cd /media/cdrom
aidan@celo:/media/cdrom$ sudo bash VBoxLinuxAdditions.run
VBoxDarwinAdditions.pkg            VBoxLinuxAdditions.run             VBoxWindowsAdditions-amd64.exe     VBoxWindowsAdditions-x86.exe       
VBoxDarwinAdditionsUninstall.tool  VBoxSolarisAdditions.pkg           VBoxWindowsAdditions.exe           
aidan@celo:/media/cdrom$ sudo bash VBoxLinuxAdditions.run 
:check 103 in vid for copy past 


to get dynamic screen resolution working

sudo apt update && sudo apt upgrade
sudo apt install build-essential linux-headers-`uname -r`

then "insert vm guest additions CD"
in terminal
cd /media/cdrom
sudo sh VBoxLinuxAdditions.run

reboot

done
