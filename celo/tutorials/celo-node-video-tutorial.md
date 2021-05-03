A Node is a conection point within a network. In Celo nodes can be set up on a client devise, be that a phone, or in this case on a virtual mashine. This tutorial shows step by step how to set up the virtual mashine in linux. How to gain sudo accsess. install the neccesay dependencys, createing the node, sending celo and more.

follow these steps in order and watch the video,for the best experience. note:this is not the only way to set up a full node. 
			  				
https://www.youtube.com/watch?v=89U866LwzBw

This node will be run on a virtual mashine
use virtual box to create and run the virtual mashine
Download and install virtual box 

the valora app on Ios/Android is very helpful for buying and trading celo, it is highly recomended for this tutorial.

download the Valora app on android/ios

debian 10 is the oporating system that this node is going to be using
Download debian 10 64 bit

Install debian 10 inside virtual box

to make important changes in your VM you need to gain sudo. this tells the mashine you are in charge and it will listin to your comands. 
here is how to Add user (aidan) to sudo group in the linux terminal.

sudo -s

su - [enter root password]

cat /etc/passwd

cat /etc/group

cat /etc/group | grep aidan

usermod -aG sudo $USER

(log in again)

id  [make sure user is in sudo group now] then...

now that you have gained sudo more comands are executable

-sudo apt install vim 

vim is a text editor that is very powerfull, but difficult to use. here is a link to help you use vim:https://www.makeuseof.com/how-to-use-vim/

--npm install -g @celo/celocli

^installing the celo client onto your VM is an essential step 

-#!/bin/bash

-set -x

-if test -f celo.env; then
      . celo.env
fi

^this script says to source in celo.env every time it is refrenced. the celo.env file will not be read in unless you explicitly tell it to be. 

curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 

^use this script to create a new folder called celo.env where you can store your keys for the nodes for easy access this step is optional but is highly recomended for quality of life and general ease of acces to you keys. 

-curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh

^use this script to create a new folder for starting the geth console/light client. You can rename this to make the new comand what ever you like to pesronalize your celo comands.  

curl -o celo.env https://gist.github.com/alchemydc/e28945f5059acd70969b39a50fd0f80a

^this link/script tells docker to source in celo.env 

making a new directory called the celo-data-dir allows you to utilize the celo.env and ../start celo.sh. you are then going to move the ../start_celo.sh folder, and the celo.env folder into this directory.(mkdir makes a new directory) (mv moves a folder into the given directory) 

mkdir celo-data-dir

mv ../celo.env .

mv ../start_celo.sh .

source celo.env


 to create your new node make sure you have followed the steps up until this comand(docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new) and 26:00 in https://www.youtube.com/watch?v=89U866LwzBw&t=280s

docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new

^this is what creates your new node 

than copy your new public account address

(vim celo.env) to use vim to edit the celo.env file and past in your new public address 

paste in you account address into celo.env using shift insert 

use (exit vim) to exit vim 

./start_celo.sh is the comand for starting the light client. this comand is created using the start_celo.sh github link 

to use the Celocli commands on your full node, you must have the celo light client running 

here are some quick helpfull comands to use on the node:

(celocli node:synced) to make sure your node is running correctly 

(celocli account:balance $ACCOUNT)this comand checks the balance on you account(you can replace $account with you public address or the name of your address in celo.env

(celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e18) whene transfering celo with this node the value of celo is represented as 1e(x)


Copy and past between the virtual mashine and your main mashine will not work. So it is vital to folow the steps below after you gain sudo to enable bidirectional copy and paste. 12:35 in https://www.youtube.com/watch?v=89U866LwzBw&t=280s shows how to enable copy and paste 



another quality of life improvment is The screen resolution. naturaly it is low res and cannot be changed. to get dynamic screen resolution skip to 36:00 in https://www.youtube.com/watch?v=89U866LwzBw&t=280s

knowing how to set up your own fullnode and light client is a great skill. A big bonus is Celo plans on setting up Celo rewards for thoes running nodes. Keep in mind this tutrial is not the only way to set up a full node. This method utilizes many usefull lynix/programing tools that can be very useful for beginer progamers and more experianced ones. I encurage experimenting with these tools and personalizing your node how you like it. Experimenting with this will expand your thinking and make you a more confident programer. thank you for reading until the end!  
