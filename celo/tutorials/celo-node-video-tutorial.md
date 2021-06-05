
  
  **Run a Celo full node in a Virtual Machine**
#
##  **Introduction**  
Full nodes are a major part of the celo blockchain. A full node is a program that will help to write, and validate new changes in the network. Multiple full nodes work together, validating the blockchain in a decentralized way. This Virtual Machines job will be to create a full node, and run a light client monitoring the network.
   
this video is to help along with the writen tutorial
  
#### *Right click this image to access the video:*
  
   [![SC2 Video](https://img.youtube.com/vi/89U866LwzBw/0.jpg)](http://www.youtube.com/watch?v=89U866LwzBw)
  
##   **Prerequisites** 
* Have a basic understanding of Virtual Machines(VM), and the linux terminal.

* Install the Valora app on ios/android:https://valoraapp.com/

* Download debian 10 64bit:https://www.debian.org/ 

* Download Oracle VM:https://www.virtualbox.org/
 
* Download Signal:https://www.signal.org/

##   *Setting up the Virtual Machine(Oracle VM)*
              
* Hit the Create New in oracle VM
* Select a dynamicaly allocated virtual hard disk
* Run the VM
* Create user 
* Select use intire disk and write changes to disk
* Check boxes: Debian desktop enviroment, SSH server, Standard system utilitys 
* Install Grub boot loader onto the Virtual HARDDISK
* Now the Virtual Machine is ready 
 
##   *Gaining sudo on the VM*
   *sudo stands for super user, once in the sudo group you have root access to the VM.* 
   *(Code is entered into the terminal of the VM)*  
    
    username@name:~$ sudo -s
    username@name:~$ su - 
    
    -enter root password
    
    username@name:~$ cat /etc/passwd
    username@name:~$ cat /etc/group | $YOURUSERNAME
    username@name:~$ usermod -aG sudo $YOURUSERNAME
    
    -enter the username you created were it says $YOURUSERNAME 
    -Restart the VM
    
    username@name:~$ id 
   check for sudo in the list of groups:
   
   ![image](https://user-images.githubusercontent.com/80616939/120870817-2f38a480-c557-11eb-9fd0-5be9e710af8d.png)

##   *Installing dependencies onto the VM using sudo commands*   
   Curl will be used for inserting github scripts into the terminal.
   
   Docker is a service used to deliver software packages. 
   
   Vim is a tool used for editing files within the terminal.
   Vim can be alot to swallow, here's a guide: https://www.linux.com/training-tutorials/vim-101-beginners-guide-vim/ 
   
    username@name:~$sudo apt update
    username@name:~$sudo apt upgrade
         
    username@name:~$sudo apt install docker.io 
    
    username@name:~$sudo apt install curl
    
    username@name:~$sudo apt install vim
##  *Enabling copy and paste between your machines*
   run an external program called VBoxLinuxAdditions witch enables bidirectional copy/paste      
    -cursor over devises in the top left and click: Insert Guest Additions CD Image 
    
    username@name:~$ cd /media/
    username@name:/media$ ls
    username@name:/media$ cd cdrom 
   
    username@name:/media/cdrom$ ls
    username@name:/media/cdrom$ sudo bash VBoxLinuxAdditions.run
    
    -Restart the VM
   Insert Guest Additions looks like:
   
 ![image](https://user-images.githubusercontent.com/80616939/118760448-0ebadb80-b830-11eb-88fa-51e672a41d83.png)
 ##  *Improving the resolution of the VM (Highly recommended)* 
   First make sure the VM is up to date, then build-essential linux-headers. Linux headers provide many vital functions without installing unnecessary files. VBoxLinuxAdditions provides the dynamic screen resolution.
   
    username@name:~$ sudo apt update && sudo apt upgrade
    username@name:~$ sudo apt install build-essential linux-headers-`uname -r'
   
    -click "insert vm guest additions CD" under devises in the top left corner
    -click cancel 
    
    username@name:~$ cd /media/cdrom
    username@name:~$ sudo sh VBoxLinuxAdditions.run
   
    -Restart the VM 
##   *Create the VM enviroment, and install the Celo client*  
   The following file adds vital functions for node.js, and installs nvm. Source in the file after adding it. 
   
    username@name:~$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
    username@name:~$ source ~/.bashrc  
   Install/use node.js version 10, and install the celo application onto this client. 
     
    username@name:~$ nvm install 10
    username@name:~$ nvm use 10
    username@name:~$ npm install -g @celo/celocli  
   The following argument makes sure that the celo.env file will utilize dependencies from node.js.
   "#!" is an interpreter directive commonly called a "shebang".
   /bin/bash is the path to interprate in bash.  
   
    username@name:~$ #!/bin/bash 
    username@name:~$ set -x
    username@name:~$ if test -f celo.env; then
           . celo.env
     fi
    +test -f celo.env   
    
    -exit     
##   *Use Curl to insert 2 github scripts, these will create 2 new files*
     
    username@name:~$ curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 
    username@name:~$ source celo.env
   This .env file is for storing and accesing enviromental variables. It will hold variables for the public keys. 
   curl uses the raw github code to instantly create the new file. Insert it again if it dosent work the first time.
   
   ![image](https://user-images.githubusercontent.com/80616939/120844550-cb998180-c52c-11eb-9503-6accb0c6a7f5.png)
     
    username@name:~$ curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh
    username@name:~$ source start_celo.sh
   "curl" in a second file for running the light client, and containing node information.
   This file utilizes docker to make it more efficient. To learn more about .env files check out this guide:https://learn.figment.io/network-documentation/extra-guides/dotenv-and-.env.
   
   ![image](https://user-images.githubusercontent.com/80616939/120844824-33e86300-c52d-11eb-8b1f-db5e66ec76bb.png)
   
##   *Move both these files into a new directory; celo-data-dir*
   The directory is called celo-data-dir.
   "mkdir" makes a new directory. "cd" will choose directory.
   "mv" moves a file into the directory. Source in files often, especialy after editing them.
   
    username@name:~$ chmod u+x ./start_celo.sh 
    username@name:~$ pwd
    
    username@name:~$ mkdir celo-data-dir
    username@name:~$ cd
    username@name:~$ cd celo-data-dir
    username@name:~/celo-data-dir$ mv ../celo.env .  
    username@name:~/celo-data-dir$ mv ../start_celo.sh .
    
    username@name:~/celo-data-dir$ source celo.env
    username@name:~/celo-data-dir$ source start_celo.sh
   ## *Create the Node, while in the celo-data-dir*
   Join the docker group using a terminal command. 
   After joining a new group always restart the VM.  
   Use id to check your groups, make sure docker is in the list of groups.
   
    username@name:~/celo-data-dir$ sudo usermod -aG docker $YOURNAME
    
    -Restart the VM
    
    username@name:~/celo-data-dir$ id
   After joining a new group always restart the VM.  
   Use id to check your groups, look for docker in this list of groups.  
    
    username@name:~/celo-data-dir$ cd celo-data-dir
    username@name:~/celo-data-dir$ docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new 
   This creates your new node account using docker.
   Copy the public address key that is provided than paste it into celo.env using vim.
    
    username@name:~/celo-data-dir$ vim celo.env
   ![image](https://user-images.githubusercontent.com/80616939/118758843-044b1280-b82d-11eb-88df-e83e5fb813f4.png)
 
   once in vim press "i" for insert mode.
   Delete YOUR_ACCOUNT_ADDRESS with Backspace.
    
   then paste in the address with "shift insert".  
    
   exit vim by pressing ":", than typing "wq" "enter". 

    username@name:~/celo-data-dir$ source celo.env 
   find celo.env, and ./start_celo.sh in your files. Their you can edit it to add more accounts, and address.
   remember to source celo.env after making changes.
   Create NODE_ADDRESS and PHONE_ADDRESS:
  
   ![image](https://user-images.githubusercontent.com/80616939/120873442-585d3300-c55f-11eb-8629-cc709ee6f0dc.png)
 
  ## *Running the light client*
   The new comand to start the light client is ./start_celo.sh.
   
   use "cat ./start_celo.sh" to find the $NAME of your node. 
   
   "docker stop $NAME" will stop the light client 
   
   Use 2 terminal tabs. One that is runing the light client, and the second for all other comands.    
   
    username@name:~/celo-data-dir$ ./start_celo.sh 
    
    username@name:~/celo-data-dir$ celocli node:synced 
    
    -true = node is working 
    
    username@name:~/celo-data-dir$ celocli account:balance NODE_ADDRESS 
    
    username@name:~/celo-data-dir$ docker stop $NAME
 
   ![image](https://user-images.githubusercontent.com/80616939/118338503-8eb11080-b4d3-11eb-99a3-11417cf79b32.png)

##   *Sending Celo to and from the node*
   
   Send both CUSD, and the CELO native token to your nodes public address 
   
   Have 2 terminal windows open one for running the light client and the other for entering comands
   
   Make sure both are in the celo-data-dir 

   cat ./start_celo.sh will read you important node information. Find the name as this is used in the next command. 
   
    username@name:~/celo-data-dir$ cat ./start_celo.sh 
 
    username@name:~/celo-data-dir$ docker exec -it $NAME geth attach
    
    -exit
    
    username@name:~/celo-data-dir$ celocli account:unlock $THE_PUBLIC_ADDRESS
    
    -enter password 
    
    username@name:~/celo-data-dir$ source celo.env 
    username@name:~/celo-data-dir$ celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e16
   before you can send CUSD be sure to have CELO native in the account for the gas fee.
   
   Understanding the unit of measurement: 1e16 = 0.01CUSD, 1e15 = 0.1CUSD, 1e14 = 1.0CUSD.
   ![image](https://user-images.githubusercontent.com/80616939/118338915-9c1aca80-b4d4-11eb-87b6-7970949923aa.png)
    
## *Conclusion*
 The fullnode and light client should now be operational. Keep in mind this tutorial is not the only way to set up a full node. This node is now under your control so remeber the keys and passwords to avoid losing any money. Please experiment with this setup and personalize it. The start_celo.sh and celo.env files are both easily customizable.
#
   **About the Author** 
   
This tutorial was put together by Aidan Dedecker. Here is my git hub page: https://github.com/Aidandedecker/Aidandedecker. here is my figment forum profile: https://community.figment.io/u/aidanbdedecker/summary.
