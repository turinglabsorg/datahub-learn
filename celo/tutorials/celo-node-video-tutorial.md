A Node is a conection point within a network. In Celo nodes can be set up on a client devise, be that a phone, or in this case on a virtual mashine. This tutorial shows step by step how to set up the virtual mashine in linux. How to gain sudo accsess. install the neccesay dependencys, createing the node, sending celo and more.

follow these steps in order and watch the video,for the best experience. note:this is not the only way to set up a full node. 
			  				
https://www.youtube.com/watch?v=89U866LwzBw

This node will be run on a virtual mashine
use virtual box to create and run the virtual mashine
Download and install virtual box 

the valora app on Ios/Android is very helpful for buying and trading celo, it is a must for this tutorial.

download the Valora app on android/ios

debian 10 is the oporating system that this node is going to be using
Download debian 10 64 bit

Install debian 10 inside virtual box

to make important changes in your VM you need to gain sudo. this tells the mashine you are in charge and it will listin to your comands. 
here is how to Add user (aidan) to sudo group in the linux terminal

sudo -s

su - [enter root password]

cat /etc/passwd

cat /etc/group

cat /etc/group | grep aidan

usermod -aG sudo $USER

(log in again)

id  [make sure user is in sudo group now] then...

now that you have gained sudo these comands are executable:

Update debian 10:”sudo apt update”, “sudo apt upgrade”

Install necessary dependencies

A.docker.io: `sudo apt install docker.io`

B. curl: `sudo apt install curl` 

curl is a help full tool for inseting links as a script 

-sudo apt install vim 

vim is a text editor that is very powerfull, but difficult to use. here is a link to help you use vim:https://www.makeuseof.com/how-to-use-vim/

C. compiler (needed to build vbox guest additions as well as celocli).  `sudo apt install build-essential linux-headers-`uname-r``

B.open vmware tools: `sudo apt install open-vm-tools-desktop`

C.node.js:

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash

source ~/.bashrc

-#!/bin/bash

-nvm install 10

-nvm use 10

--npm install -g @celo/celocli

^install the celo client onto your VM

-#!/bin/bash

-set -x

-if test -f celo.env; then
      . celo.env
fi

^this script tells celo.env to source in celo.env every time it is refrenced 

curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 

^use this link/script to create a new folder called celo.env where you can store your keys for the nodes for easy access

-curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh

^use this link/script to create a new folder for starting the geth console/light client. You can rename this to make the new comand what ever you like 

curl -o celo.env https://gist.github.com/alchemydc/e28945f5059acd70969b39a50fd0f80a

^this link/script tells docker to source in celo.env 

chmod u+x start_celo.sh

pwd

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash

curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env

now you are going to make a new directory and called the celo-data-dir. you are then going to move the ../start_celo.sh folder, and the celo.env folder into this directory 

mkdir celo-data-dir

cd celo-data-dir

mv ../celo.env .

mv ../start_celo.sh .

source celo.env

sudo usermod -aG docker aidan

id 

restart

id

now its time to create a new node 

cd celo-data-dir/

source celo.env

echo $CELO_IMAGE

cat celo.env

docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new

^this comand creates your new node 

copy your account address

vim celo.env

now paste in you account address into celo.env using shift insert 

exit vim

source celo.env

curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh

./start_celo.sh

^./start_celo.sh is the comand for starting the light client 

Create shell wrapper for Celo environment: 

Start the docker image (light node) `./start_celo.sh`

here are some Celocli commands to check the integrity full node:

celocli node:synced

^must have the light client runing to run these comands 

celocli account:list

celocli account:balance $ACCOUNT

^this comand checks the balance on you account(you can replace $account with you public address or the name of your address in celo.env) 

To send funds back from full node to phone, we need to unlock the key

Start geth console

docker exec -it celo-fullnode geth attach

celocli account:unlock $

^to unlock your account you need to put in your password 


Send from node to phone:

celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e18
 
                                                                   ^all units are expressed in 1e18



Copy and past between the virtual mashine and your main mashine will not work. So it is vital to folow the steps below after you gain sudo to enable bidirectional copy and paste.  

aidan@celo:~$ cd /media/cdrom

cdrom/  cdrom0/ 

aidan@celo:~$ cd /media/cdrom

aidan@celo:/media/cdrom$ sudo bash VBoxLinuxAdditions.run
VBoxDarwinAdditions.pkg            VBoxLinuxAdditions.run             VBoxWindowsAdditions-amd64.exe     VBoxWindowsAdditions-x86.exe       
VBoxDarwinAdditionsUninstall.tool  VBoxSolarisAdditions.pkg           VBoxWindowsAdditions.exe           
aidan@celo:/media/cdrom$ sudo bash VBoxLinuxAdditions.run


The natural screen resolution is low res and cannot be changed. to get dynamic screen resolution follow these steps 

to get dynamic screen resolution working

sudo apt update && sudo apt upgrade

sudo apt install build-essential linux-headers-`uname -r`

then "insert vm guest additions CD"

in terminal

cd /media/cdrom

sudo sh VBoxLinuxAdditions.run

reboot

done

knowing how to set up your own fullnode and light client is a great skill. One bounes is Celo plans on setting up Celo rewards for thoes running nodes. This tutrial is not the only way to set up a full node. This method utilizes many usefull lynix/programing tools that can be very useful for beginer progamers and more experianced ones. I encurage experimenting with these tools and personalizing your node how you like it. Experimenting will this will expand your thinking and make you a more confedent programer. thank you for reading until the end!  
