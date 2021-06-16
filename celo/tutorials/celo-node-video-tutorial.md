
  
  Run a Celo full node in a Virtual Machine
#
#  Introduction 
Full nodes are a major part of the celo blockchain. A full node is a program that will help to write, and validate new changes in the network. Multiple full nodes work together, validating the blockchain in a decentralized way. This Virtual Machines job will be to create a full node, and run a light client monitoring the network.
   
Watch the videos and follow along with the writen tutorial.
  
#### Right click image to access the video:
  #### Video 1, Creating the VM
   [![SC2 Video](https://img.youtube.com/vi/Cdqwzf-zfug/0.jpg)](http://www.youtube.com/watch?v=Cdqwzf-zfug)
  #### Video 2, Building the fullnode
   [![SC2 Video](https://img.youtube.com/vi/tQVghp8aQZQ/0.jpg)](http://www.youtube.com/watch?v=tQVghp8aQZQ)
#   Prerequisites 
* Have a basic understanding of Virtual Machines(VM), and the linux terminal.

* Install the Valora app on ios/android:https://valoraapp.com/

* Download debian 10 64bit:https://www.debian.org/ 

* Download Oracle VM:https://www.virtualbox.org/
 
* Download Signal:https://www.signal.org/

#   Creating the VM
              
* Hit the Create New in oracle VM
* Select a dynamicaly allocated virtual hard disk
* Run the VM
* Create user 
* Select use entire disk and write changes to disk
* Check boxes: Debian desktop environment, SSH server, Standard system utilities 
* Install Grub boot loader onto the Virtual HARDDISK
* Now the Virtual Machine is ready 
 
##   Gain sudo on the VM
   #### The term "sudo" stands for super user. Once in the sudo group you have root access to the VM. 
   
 #### (Code is entered into the VM terminal)
 
 ``` su -  ``` 
    
   - Root password
 
 ``` usermod -aG sudo $YOURUSERNAME ```
     
   - Restart the VM
    
 ``` id ```
 
   - Find (sudo) in the list:
   
   ![image](https://user-images.githubusercontent.com/80616939/120870817-2f38a480-c557-11eb-9fd0-5be9e710af8d.png)

##   Install dependencies   
   #### Curl will be used for inserting github scripts into the terminal.
   
   #### Docker is a service used to deliver software packages. 
   
   #### Vim is a tool used for editing files within the terminal. Here's a guide on Vim: https://www.linux.com/training-tutorials/vim-101-beginners-guide-vim/ 
   
  ``` sudo apt update ```
  
  ``` sudo apt upgrade ```
         
  ``` sudo apt install docker.io ```
    
  ``` sudo apt install curl ```
    
  ``` sudo apt install vim ```
  
##  Enable bidirectional copy/paste 
   #### Run an external program called VBoxLinuxAdditions, which enables bidirectional copy/paste.
   
   - Cursor over devices in the top left corner, and select: Insert Guest Additions CD Image 
    
  ``` cd /media/ ```
  
  ``` ls ```
  
  ``` cd cdrom ```
   
  ``` ls ``` 
  
  ``` sudo bash VBoxLinuxAdditions.run ```
    
   - Restart the VM
    
   - Enable Bidirectional copy/paste under devices in shared clipboard 
   
 ![image](https://user-images.githubusercontent.com/80616939/118760448-0ebadb80-b830-11eb-88fa-51e672a41d83.png)
 ##  Improve the VM resolution  
   #### Linux headers provide many vital functions without installing unnecessary files. VBoxLinuxAdditions provides the dynamic screen  resolution.
  
  ``` sudo apt install build-essential linux-headers-`uname -r` ```
   
   - Click "insert vm guest additions CD" under devices in the top left corner
   - Cancel 
    
  ``` cd /media/cdrom ```
  
  ``` sudo sh VBoxLinuxAdditions.run ```
   
   - Restart the VM 
##   Create the environment, join Docker, and install the Celo client  
   #### The following file adds functionality to node.js, and installs nvm. Source in files after adding them. 
   
  ``` curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash ```
  
  ``` source ~/.bashrc ``` 
 # 
   #### Install/use node.js version 10, then install the celo application onto this client.  
   
  ``` nvm install 10 ```
  
  ``` nvm use 10 ``` 
  
  ``` npm install -g @celo/celocli ``` 
  #
   #### Next join into the Docker group.
  
  ``` sudo usermod -aG docker $YOURNAME ```
  
   - Restart VM
  
  ``` id ```
  
   - Check for (docker)   
  # 
   #### The interpreter directive, "#!" is called a "shebang". The path, "/bin/bash" sets the terminal to interpret in bash.  
   
  ``` #!/bin/bash ```
  
  ``` set -x ```
  
   #### The following argument makes sure that the celo.env file will be loaded into the terminal. 
 
 ```
  if test -f celo.env; then 
           . celo.env
     fi
    +test -f celo.env   
 ```
   - Close this terminal     
##  Use Curl to create 2 new files
   #### To learn about .env files check out this guide: https://learn.figment.io/network-documentation/extra-guides/dotenv-and-.env.
   
   #### Curl uses the raw github code, and instantly creates the new files.
   
 - This .env file is for storing and accessing sensitive environment variables. It will hold variables for the public keys. 
 
  ``` 
  curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 
  ```
  
  ``` source celo.env ```
   
   ![image](https://user-images.githubusercontent.com/80616939/120844550-cb998180-c52c-11eb-9503-6accb0c6a7f5.png)
#
 - The second file runs the light client, and contains information about the node. This file requires Docker.
  ``` 
  curl -o start_celo.sh https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh 
  ```
  
  ``` source start_celo.sh ```
  
   ![image](https://user-images.githubusercontent.com/80616939/120844824-33e86300-c52d-11eb-8b1f-db5e66ec76bb.png)
   
##  Move these files into a new directory; celo-data-dir
   - The directory will be called "celo-data-dir".
   
   - "mkdir" makes a new directory. 
   
   - "cd" choose the directory.
   
   - "mv" moves a file into the directory. 
    
  ``` mkdir celo-data-dir ```
  
  ``` cd ```
  
  ``` cd celo-data-dir ```
  
  ``` mv ../celo.env . ``` 
  
  ``` mv ../start_celo.sh . ```
    
  ``` source celo.env ```
  
  ``` source start_celo.sh ```
  - Source in files after making changes
## Create the Node, while in the celo-data-dir 
    
  ``` cd celo-data-dir ```
  
  ``` docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new ```
  
   - Copy the public address key that is provided with your new node account.
    
  ``` vim celo.env ```
  
   ![image](https://user-images.githubusercontent.com/80616939/118758843-044b1280-b82d-11eb-88df-e83e5fb813f4.png)
 
   - In vim press "i" for insert mode.
  
   - Delete YOUR_ACCOUNT_ADDRESS with Backspace.
    
   - Paste in the address with "shift insert".  
    
   - Exit vim by pressing ":", then typing "wq" "enter". 

  ``` source celo.env ```
 
 ## Running the light client
   ``` ./start_celo.sh. ```, Starts the light client.
   
   ``` cat ./start_celo.sh ```, Displays node information. 
   
   ``` docker stop geth ```, Stops the light client. 
   
   - Use 2 terminal tabs. One for starting the light client, and the second for all other commands.    
   
  ``` ./start_celo.sh ``` 
    
  ``` celocli node:synced ```
    
   - If true the node is connected.   
    
  ``` celocli account:balance NODE_ADDRESS ```
    
  ``` docker stop geth ```
 
   ![image](https://user-images.githubusercontent.com/80616939/118338503-8eb11080-b4d3-11eb-99a3-11417cf79b32.png)

## Sending Celo to and from the node
   
   - Send both CUSD, and CELO native token to your node address. 
   
   - Have 2 terminal windows open. One is for running the light client, and the other for entering commands.
   
   - Make sure both terminals are in the celo-data-dir. 

  ``` cat ./start_celo.sh ```
 
  ``` docker exec -it geth geth attach ```
    
  ``` exit ```
    
  ``` celocli account:unlock $PUBLIC_ADDRESS ```
    
   - Enter password 
    
  ``` source celo.env ```
  
  ``` celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e16 ```
   
   #### Understanding this unit of measurement: 1e16 = 0.01CUSD, 1e15 = 0.1CUSD, 1e14 = 1.0CUSD.
   
   ![image](https://user-images.githubusercontent.com/80616939/118338915-9c1aca80-b4d4-11eb-87b6-7970949923aa.png)
    
## Conclusion
 The fullnode and light client should now be operational. Keep in mind this tutorial is not the only way to set up a full node. This node is now under your control so remember the keys and passwords to avoid losing any money. Please experiment with this setup and personalize it. The start_celo.sh and celo.env files are both easily customizable.
#
 ###  About the Author 
   
This tutorial was put together by Aidan Dedecker. Here is my git hub page: https://github.com/Aidandedecker/Aidandedecker. here is my figment forum profile: https://community.figment.io/u/aidanbdedecker/summary.
