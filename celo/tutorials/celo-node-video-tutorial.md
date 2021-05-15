   **HOW TO SET UP A CELO FULL NODE ON A VIRTUAL MASHINE**
#

   *INTRODUCTION*
                                           
  This tutorial is a start to finish guide on setting up a Celo Full node. Full nodes are an important part of the celo ecosystem. The node bridges the gap between light clients and the validator nodes. full node incentives are one of the ways CELO encurages their use. 
#

   *DESCRIPTION*
   
   ![image](https://user-images.githubusercontent.com/80616939/118338023-5d841080-b4d2-11eb-9e4a-ac3ce97267bf.png)

   
   A Full Node uses a client devise; (in this case the VM, and your smart phone) along with the celo application to create a node
   
  This method of setting up a full node utilizes Debian 10, lynux, and is built on the terminal of the VM. I created this video to help with the writen tutorial
  
  Right click this image to access the video:
  
   [![SC2 Video](https://img.youtube.com/vi/89U866LwzBw/0.jpg)](http://www.youtube.com/watch?v=89U866LwzBw)
  
   In this tutorial I will demonstrate:
  
  sending and reciving CUSD/CELO tokens 
  
  building a CELO fullnode 
  
  running a light client 
  
  using .env files 
  
  improving the screen resolution of the VM
  
  Utilizing the Valora mobile app 
  
  Gaining sudo on the VM
  
  Enabling bidirectional copy and paste on the VM 
  
  and more
 
#
   *PREREQUISITES* 
                 
>Install the Valora app on ios/android

>Download debian 10 64bit 

>Downlaod Oracle VM VirtualBox

>have CUSD and CELO in your Valora wallet
 
>download Signal(for sending wallet addreses)on your pc and smart phone

#
   *STEPS FOR SETTING UP THE VIRTUAL MASHINE*
              
* Hit the Create New in oracle VM (to create a new virtual mashine) 
* select a dynamicaly allocated virtual hard disk
* run the VM
* Create user 
* select use intire disk and write changes to disk
* Check boxes: Debian desktop enviroment, SSH server, Standard system utilitys 
* install Grub boot loader onto the Virtual HARDDISK
* Now the Virtual Mashine is ready 
#
 *Code is entered into the terminal of the VM* 
#
   *GAINING SUDO ON THE VM (enter the folowing comands in order into terminal)*
   *sudo stands for super user, once you gain sudo you essentialy gain full control* 
     
    username@name:~$ sudo -s
    username@name:~$ su - 
         //enter root password
    username@name:~$ cat /etc/passwd
    username@name:~$ cat /etc/group | $YOURUSERNAME
    username@name:~$ usermod -aG sudo $YOURUSERNAME
    -Restart the VM
    username@name:~$ id 
         //id allows you to see all the groups you are in 
         //sudo is the group you are looking for  
   ![image](https://user-images.githubusercontent.com/80616939/118337789-bc955580-b4d1-11eb-886b-25af21a76f97.png)

#
   *INSTALLING DEPENDENCY ONTO THE VM USING SUDO COMANDS*   
   
    username@name:~$sudo apt update
    username@name:~$sudo apt upgrade
         //these comands are only posible once in the sudo group 
    username@name:~$sudo apt install docker.io
    username@name:~$sudo apt install curl
         //curl will be used to insert git hub links 
    username@name:~$sudo apt install vim
         //vim is a tool used for text editing               
#           
   *ENABLING COPY AND PASTE IN AND OUT OF THE VM (ENTER THESE COMANDS INTO THE TERMINAL)*
   
  ![image](https://user-images.githubusercontent.com/80616939/118339670-c8cfe180-b4d6-11eb-8964-7c6c466b8d86.png)
         
    -cursor over devises in the top left and click: Insert Guest Additions CD Image 
    username@name:~$ cd /media/
    username@name:/media$ ls
    username@name:/media$ cd cdrom
    username@name:/media/cdrom$ ls
    username@name:/media/cdrom$sudo bash VBoxLinuxAdditions.run
         //make sure everything is capitalized correctly 
    -Restart the VM
         //now bidirectional copy and past will work
#
   *CREATING THE VM ENVIROMENT (ENTER THESE COMANDS INTO THE TERMINAL)*
 
    username@name:~$ sudo apt install build-essential linux-headers-`uname-r`
         //linux headers add vital functions with/out installing alot of unnecesary files 
    username@name:~$ sudo apt install open-vm-tools-desktop
         // VM tools are not requiered but can be helpful
    username@name:~$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
    username@name:~$ source ~/.bashrc
    username@name:~$ #!/bin/bash
    username@name:~$ nvm install 10
    username@name:~$ nvm use 10
         //this install the latest Node Version Manager 
    username@name:~$ npm install -g @celo/celocli
         // now install the celo application onto this client 
    username@name:~$ #!/bin/bash
    username@name:~$ set -x
    username@name:~$ if test -f celo.env; then
           . celo.env
     fi
    +test -f celo.env   
         //the file called celo.env we are about to create will now be utilized more efficiently   
 #
   *NOW add 2 NEW FILES with curl: celo.env(for storing and accesing important keys), AND start_celo.sh(for using the light client)*
     
    username@name:~$ curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 
         //.env files are for storing and accesing enviromental variables, in this case creating variables for the public keys 
    username@name:~$ curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh
         // this creates a new file for running the light client and storing node information 
    username@name:~$ curl -o celo.env https://gist.github.com/alchemydc/e28945f5059acd70969b39a50fd0f80a
    username@name:~$ chmod u+x start_celo.sh
    username@name:~$ pwd
    
  to learn more about these .env files check out this guide:https://learn.figment.io/network-documentation/extra-guides/dotenv-and-.env 
 #
   *TO ACCESS THESE FILES EASILY MOVE THEM INTO A NEW DIRECTORY CALLED; celo-data-dir*
   
  ![image](https://user-images.githubusercontent.com/80616939/118339243-78a44f80-b4d5-11eb-8aa4-ae69df82f50c.png)

    username@name:~$ mkdir celo-data-dir
         // mkdir makes a new directory 
    username@name:~$ cd celo-data-dir
    username@name:~/ celo-data-dir$ mv ../celo.env .
         // mv moves the file into the directory  
    username@name:~/ celo-data-dir$ mv ../start_celo.sh .
    username@name:~$ celo-data-dir$ source celo.env
         // source in both the files so they are up to date 
    username@name:~$ celo-data-dir$ source start_celo.sh
         // now these files can be utilized while in the celo-data-dir 
 # 
   *NOW WHILE IN THE celo,data,dir CREATE THE NODE AND PASTE THE KEYS INTO celo.env*
   
  ![image](https://user-images.githubusercontent.com/80616939/118339850-54497280-b4d7-11eb-9925-8039488bc761.png)
    
    username@name:~/ celo-data-dir$ sudo usermod -aG docker $YOURNAME
    -Restart the VM
    username@name:~/ celo-data-dir$ id (check if you have joined the docker group)
    username@name:~/ celo-data-dir$ cd celo-data-dir
         //now you are using the celo-data-dir 
    username@name:~/ celo-data-dir$ source celo.env
    username@name:~/ celo-data-dir$ cat celo.env
    username@name:~/ celo-data-dir$ docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new (this comand creates your new node)
         //this comand creates your new node
    -Copy you public address key so you can paste it into celo.env once in vim
    username@name:~/ celo-data-dir$ vim celo.env
    -paste in the address into "YOUR_ACCOUNT_ADDRESS" using shift insert, here is a website to help you navigate vim https://www.makeuseof.com/how-to-use-vim/)
    -exit vim by pressing : than typing wq enter 
    username@name:~/ celo-data-dir$ source celo.env 
         //every time you make a change to celo.env you must source it in
  find celo.env in Files under celo-data-dir. Their you can edit it to add more accounts, and address.
  
  remember to source celo.env after making changes.
  
  ![image](https://user-images.githubusercontent.com/80616939/118337560-498bdf00-b4d1-11eb-8aa3-41857f1079b1.png)

 # 
   *RUNNING THE LIGHT CLIENT*
   
![image](https://user-images.githubusercontent.com/80616939/118338503-8eb11080-b4d3-11eb-99a3-11417cf79b32.png)

    username@name:~/ celo-data-dir$ ./start_celo.sh (this is the new comand to start the light client)
    -Create new window in terminal while light client is running to enter the following comands 
    username@name:~/ celo-data-dir$ celocli node:synced 
         //if true your light client is synced with the node and running properly
    username@name:~/ celo-data-dir$ celocli account:list 
    username@name:~/ celo-data-dir$ celocli account:balance $ACCOUNT
         // where it says $ACCOUNT enter the name that is in celo.env or the  
    username@name:~/ celo-data-dir$ docker stop geth
         //this stops the light client  
#
   *SENDING FUNDS TO AND FROM THE NODE*
   
    -first send both CUSD, and the CELO native token to your nodes public address 
    username@name:~/ celo-data-dir$ start light client in seprate tab 
    username@name:~/ celo-data-dir$ cat ./start_celo.sh 
    -Find the name(geth), represented in the next command as $NAME
    username@name:~/ celo-data-dir$ docker exec -it $NAME geth attach
    username@name:~/ celo-data-dir$ exit
    username@name:~/ celo-data-dir$ celocli account:unlock $PUBLIC ADDRESS
    -enter password 
    username@name:~/ celo-data-dir$ celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e16 (this will send the CUSD from this node to another address 1e16=0.01CUSD) 
         //this will send the CUSD from this node to another address 
    -(1e16 = 0.01CUSD)(1e15 = 0.1CUSD)
 ![image](https://user-images.githubusercontent.com/80616939/118338915-9c1aca80-b4d4-11eb-87b6-7970949923aa.png)

#
   *IMPROVING THE SCREEN RESOLUTION OF THE VM (Highly recommended)* 
   
    username@name:~$ sudo apt update && sudo apt upgrade
    username@name:~$ sudo apt install build-essential linux-headers-`uname -r`
    -then "insert vm guest additions CD" under devises in the top left corner 
    username@name:~$ cd /media/cdrom
    username@name:~$ sudo sh VBoxLinuxAdditions.run
    -Restart the VM 
    -now you can strech the screen from the corners and the resolution will change 
#

after completing all the vital steps the fullnode and light client are ready to go. Keep in mind this tutrial is not the only way to set up a full node. This node is now under your control so remeber the keys and passwords to avoid losing any money. Please experiment with this setup and personalize it. If you run into any issues please contact me on Discord; Aidan68#9447. 
#
   **About the Author** 
   
this content was created by Aidan Dedecker and Alchemy Dc. Reach out to us on Discord; Aidan68#9447, alchemydc#8013. Heres a short background on me. I'm a high school student out of the USA here to contribute, and do somthing productive. My goal is for this tutorial to be intagrated into Figment learn.   
   
