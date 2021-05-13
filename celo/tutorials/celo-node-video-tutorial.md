   HOW TO SET UP A CELO FULL NODE ON A VIRTUAL MASHINE
#

   INTRODUCTION
                                           
  This tutorial is a start to finissh guide on setting up a Celo Full node. Full nodes are an important part of the celo ecosystem. The node bridges the gap between light clients and the validator nodes. full node incentives are one of the ways CELO encurages their use. 
#

   DESCRIPTION
   
   ![image](https://user-images.githubusercontent.com/80616939/117864141-b6914180-b251-11eb-8865-1e4b59faeee9.png)
   
   A Full Node uses a client devise; (in this case the VM, and your smart phone) along with the celo application to create a node

   
   
  This method of setting up a full node utilizes Debian 10, lynux, and is built on the terminal of the VM. I created this video to help with the writen tutorial
  
  https://www.youtube.com/watch?v=89U866LwzBw&t=736s
  
  here are some of the things that will be demonstated in this tutorial:
  
  >sending and reciving CUSD/CELO tokens 
  
  >building a CELO fullnode 
  
  >runing a light client 
  
  >Saving multiple public addresses 
  
  >improving the resolution of the VM
  
  >Utilizing the Valora mobile app 
  
  >Gaining sudo on the VM
  
  >Enabling bidirectional copy and paste on the VM 
 
#
   PREREQUISITES 
                 
>Install the Valora app on ios/android

>Download debian 10 64bit 

>Downlaod Oracle VM VirtualBox

>have CUSD and CELO in your Valora wallet
 
>download Signal(for sending wallet addreses)on your pc and smart phone

#
   SETTING UP THE VIRTUAL MASHINE
              
* Hit the Create New in oracle VM (to create a new virtual mashine) 
* select a dynamicaly allocated virtual hard disk
* run the VM
* Create user 
* select use intire disk and write changes to disk
* Check boxes: Debian desktop enviroment, SSH server, Standard system utilitys 
* install Grub boot loader onto the Virtual HARDDISK
* Now the Virtual Mashine is ready 
#
 Each * represents an individual comand into the terminal
#
   GAINING SUDO ON THE VM (enter the folowing comands in order into terminal)
      
    * sudo -s
    * su - 
         //enter root password
    * cat /etc/passwd
    * cat /etc/group | $YOURUSERNAME
    * usermod -aG sudo $YOURUSERNAME
         //restart
    * id (to check if in the sudo group)
#
   INSTALLING DEPENDENCY ONTO THE VM USING SUDO COMANDS
   
    * sudo apt update
    * sudo apt upgrade
         //make sure the VM is up to date
    * sudo apt install docker.io
    * sudo apt install curl
         //curl will be used to insert git hub links 
    * sudo apt install vim
         //vim is a tool used for text editing               
#           
   ENABLING COPY AND PASTE IN AND OUT OF THE VM (ENTER THESE COMANDS INTO THE TERMINAL)
           
         //cursor over devises in the top left and click: Insert Guest Additions CD Image 
    * cd /media/
    * ls
    * cd cdrom
    * ls
    * sudo bash VBoxLinuxAdditions.run
    * Restart the VM
         //now bidirectional copy and past will work
#
   CREATING THE VM ENVIROMENT (ENTER THESE COMANDS INTO THE TERMINAL)
 
    * sudo apt install build-essential linux-headers-`uname-r`
         //linux headers add vital functions with/out installing alot of unnecesary files 
    * sudo apt install open-vm-tools-desktop
         // VM tools are not requiered but can be helpful
    * curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
    * source ~/.bashrc
    * #!/bin/bash
    * nvm install 10
    * nvm use 10
         //this install the latest Node Version Manager 
    * npm install -g @celo/celocli
         // now install the celo application onto this client 
    * #!/bin/bash
    * set -x
    * if test -f celo.env; then
          . celo.env
     fi
          -  curl -o celo.env  
         //this argument reads in celo.env when it is sourced  
 #
   NOW INSERT 2 NEW FILES: celo.env(for storing and accesing keys), AND start_celo.sh(witch is the new command for starting the light client)
        
    * curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 
         //this creates a new file for storing and accesing public keys 
    * curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh
         // this creates a new file for running the light client and storing node information 
    * curl -o celo.env https://gist.github.com/alchemydc/e28945f5059acd70969b39a50fd0f80a
    * chmod u+x start_celo.sh
    * pwd
 #
   THESE FILES WILL BE MOVED INTO A BRAND NEW DIRECTORY CALLED celo-data-dir
  
    * mkdir celo-data-dir
         // mkdir makes a new directory 
    * cd celo-data-dir
    * mv ../celo.env .
         // mv moves the file into the directory  
    * mv ../start_celo.sh .
    * source celo.env 
         // now these files can be utilized while in the celo-data-dir 
 # 
   NOW IT'S TIME TO CREATE THE NODE
      
    * sudo usermod -aG docker $YOURNAME
         //restart
    * id (check if you have joined the docker group)
    * cd celo-data-dir
    * source celo.env
    * cat celo.env
    * docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new (this comand creates your new node)
         //this comand creates your new node
         //copy you public address key so you can paste it into celo.env using vim
    * vim celo.env
         //paste in the address key using shift insert, here is a website to help you navigate vim https://www.makeuseof.com/how-to-use-vim/)
         //exit vim by pressing : than typing wq enter 
    * source celo.env 
         //every time you make a change to celo.env you must source it in
 # 
   RUNNING THE LIGHT CLIENT
    
    * ./start_celo.sh (this is the new comand to start the light client)
         //enter the following in a seprate terminal tab while the light client is running
    * celocli node:synced (if true your light client is running properly)
         //if true your light client is running properly
    * celocli account:list 
    * celocli account:balance $ACCOUNT
    * docker stop geth
         //this stops the light client  
#
   SENDING FUNDS TO AND FROM THE NODE
   
         //send CUSD, and the CELO native token to your nodes public address 
    * start light client in seprate tab 
    * cat ./start_celo.sh (find the name, represented in the next command as $NAME)
    * docker exec -it $NAME geth attach
    * exit
    * celocli account:unlock $PUBLIC ADDRESS
         //enter password 
    * celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e16 (this will send the CUSD from this node to another address 1e16=0.01CUSD) 
         //this will send the CUSD from this node to another address (1e16 = 0.01CUSD)
#
   IMPROVING THE SCREEN RESOLUTION OF THE VM
   
    * sudo apt update && sudo apt upgrade
    * sudo apt install build-essential linux-headers-`uname -r`
         //then "insert vm guest additions CD" under devises in the top left corner 
         //return to terminal
    * cd /media/cdrom
    * sudo sh VBoxLinuxAdditions.run
         //reboot
         //now the screen resolution is dynamic 
#
![image](https://user-images.githubusercontent.com/80616939/118052031-482aad00-b33f-11eb-9195-6289f79dde26.png)


after completing all the steps the fullnode and light client are ready to go. Keep in mind this tutrial is not the only way to set up a full node. This node is now under your control so remeber the keys and passwords to avoid losing any money. Please experiment with this setup and personalize it. If you run into any issues please contact me on Discord; Aidan68#9447. 
#
   About the Author 
   
this content was created by Aidan Dedecker and Alchemy Dc. Reach out to us on Discord; Aidan68#9447, alchemydc#8013. Im a high school student here to contribute what i can, and do somthing productive.    
   
