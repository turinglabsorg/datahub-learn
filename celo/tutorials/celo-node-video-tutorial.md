                                         HOW TO SET UP A CELO FULL NODE ON A VIRTUAL MASHINE
#

   INTRODUCTION
                                           
  Full nodes are an important part of the celo ecosystem. The node bridges the gap between light clients and the validator nodes. full node incentives are one of the ways CELO encurages their use. This method of setting up a full node utilizes Debian 10, lynux, and is built using the terminal of the VM. Here is the video to go along with the tutorial https://www.youtube.com/watch?v=89U866LwzBw&t=736s
  
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
   PREREQUISITES TO INSTALL
                 
>Install the Valora app on ios/android

>Download debian 10 64bit 

>Downlaod Oracle VM VirtualBox
#
   SETTING UP THE VIRTUAL MASHINE
              
* Hit the Create New in oracle VM (to create a new virtual mashine) 
* select a dynamicaly allocated virtual hard disk
* run the VM
* Create user 
* select use intire disk and write changes to disk
* Check boxes: Debian desktop enviroment, SSH server, Standard system utilitys 
* install Grub boot loader onto the Virtual HARDDISK
* Now the Virtual Mashin is ready to go 
#

   GAINING SUDO ON THE VM
    
    //Go into the terminal and enter the folowing in order  
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
    * sudo apt install docker.io
    * sudo apt install curl
    * sudo apt install vim             
#           
   ENABLING COPY AND PASTE IN AND OUT OF THE VM (ENTER THESE COMANDS INTO THE TERMINAL)
           
    //cursor over devises in the top left and click: Insert Guest Additions CD Image 
    * cd /media/
    * ls
    * cd cdrom
    * ls
    * sudo bash VBoxLinuxAdditions.run
    //Restart the VM
#
   CREATING THE VM ENVIROMENT (ENTER THESE COMANDS INTO THE TERMINAL)
 
    * sudo apt install build-essential linux-headers-`uname-r`
    * sudo apt install open-vm-tools-desktop
    * curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
    * source ~/.bashrc
    * #!/bin/bash
    * nvm install 10
    * nvm use 10
    * npm install -g @celo/celocli
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
    * curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh
    * curl -o celo.env https://gist.github.com/alchemydc/e28945f5059acd70969b39a50fd0f80a
    * chmod u+x start_celo.sh
    * pwd
 #
   THESE FILES WILL BE MOVED INTO A BRAND NEW DIRECTORY CALLED celo-data-dir
  
    * mkdir celo-data-dir
    * cd celo-data-dir
    * mv ../celo.env . 
    * mv ../start_celo.sh .
    * source celo.env 
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
    done
#




 


             

              
             







  






after following theses steps the fullnode and light client are ready to go. Keep in mind this tutrial is not the only way to set up a full node. Please experiment with this setup and personalize it. thank you for reading until the end!  
