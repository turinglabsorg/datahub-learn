####   *Description*
  
  **HOW TO SET UP A CELO FULL NODE ON A VIRTUAL MASHINE**
#
##  **Introduction**  
  This tutorial is a start to finish guide on setting up a Celo Full node. Full nodes are an important part of the celo ecosystem. The node bridges the gap between light clients and the validator nodes. full node incentives are one of the ways CELO encurages their use. This Virtual Mashines job will be to set up, run, and manage your full Node.
   
   A Full Node uses a client devise; (in this case the VM, and your smart phone) along with the celo application 
   
  This method of setting up a full node utilizes Debian 10, lynux, and is built on the terminal of the VM. I created a video to help with the writen tutorial
  
#### *Right click this image to access the video:*
  
   [![SC2 Video](https://img.youtube.com/vi/89U866LwzBw/0.jpg)](http://www.youtube.com/watch?v=89U866LwzBw)
  
#### *This full node will be able to do the following things:*
  
  Sending and reciving CUSD/CELO tokens 
  
  Running a light client 
  
  Using .env files for the important keys
  
  Creating multiple Celo accounts 
  
##   **Prerequisite** 
you should have a basic understanding of Virtual Mashines(VM), and the linux terminal, also make sure you have some CELO/CUSD in your possesion. Here are the thing to install before getting started: 

* Install the Valora app on ios/android

* Download debian 10 64bit 

* Downlaod Oracle VM VirtualBox
 
* Download Signal(for sending wallet addreses)on your pc and smart phone

##   *Steps for setting up the Virtual Mashine(VM)*
              
* Hit the Create New in oracle VM (to create a new virtual mashine) 
* select a dynamicaly allocated virtual hard disk
* run the VM
* Create user 
* select use intire disk and write changes to disk
* Check boxes: Debian desktop enviroment, SSH server, Standard system utilitys 
* install Grub boot loader onto the Virtual HARDDISK
* Now the Virtual Mashine is ready 
 
##   *Gaining sudo on the VM*
   *sudo stands for super user, once you gain sudo you essentialy gain full control.* 
   *(Code is entered into the terminal of the VM)*  
    
    username@name:~$ sudo -s
    username@name:~$ su - 
    -enter root password
    
    username@name:~$ cat /etc/passwd
    username@name:~$ cat /etc/group | $YOURUSERNAME
    # this to adds yourself to the sudo group 
    username@name:~$ usermod -aG sudo $YOURUSERNAME
    # make sure to enter the username you created were it says $YOURUSERNAME 
    
    -Restart the VM
    # for changes in group to set in you have to first restart the VM
    username@name:~$ id 
    
    # id allows you to see all the groups you are in 
    # sudo is the group you are looking for  
   ![image](https://user-images.githubusercontent.com/80616939/118337789-bc955580-b4d1-11eb-886b-25af21a76f97.png)

##   *Installing dependencys onto the VM using sudo commands*   
   
    username@name:~$sudo apt update
    username@name:~$sudo apt upgrade
    # these sudo comands are only posible once in the sudo group 
         
    username@name:~$sudo apt install docker.io
    # docker will be used to run the celo application 
    
    username@name:~$sudo apt install curl
    # curl will be used to insert git hub links 
    
    username@name:~$sudo apt install vim
    # vim is a tool used for editing files within the terminal  
    # vim is difficult to learn, but can be much faster  
        
##  *Enabling copy and paste between your mashines*
         
    -cursor over devises in the top left and click: Insert Guest Additions CD Image 
    username@name:~$ cd /media/
    username@name:/media$ ls
    username@name:/media$ cd cdrom
    # the media directory is used for certain external programs 
   
    username@name:/media/cdrom$ ls
    username@name:/media/cdrom$sudo bash VBoxLinuxAdditions.run
    # make sure everything is capitalized correctly 
    -Restart the VM
    # now bidirectional copy and past will work
 Insert Guest Additions:        
 ![image](https://user-images.githubusercontent.com/80616939/118760448-0ebadb80-b830-11eb-88fa-51e672a41d83.png)

##   *Creating the VM enviroment and installing the Celo client*
 
    username@name:~$ sudo apt install build-essential linux-headers-`uname-r`
    # linux headers add vital functions with/out installing alot of unnecesary files 
         
    username@name:~$ sudo apt install open-vm-tools-desktop
    # VM tools are not requiered but can be helpful
    
    # this github script installs important dependencys for using this node 
    username@name:~$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash
    username@name:~$ source ~/.bashrc
    # source in files when first adding them or making changes 
    username@name:~$ #!/bin/bash
    # #!/bin/bash tells the terminal what language to interprate 
    # #!/bin/python would interprate the python language 
    
    username@name:~$ nvm install 10
    username@name:~$ nvm use 10
    # this installs and uses the latest Node Version Manager
    
    username@name:~$ npm install -g @celo/celocli
    # install the celo application onto this client 
         
    username@name:~$ #!/bin/bash
    # this tells the terminal to interprate the following script in bash 
    username@name:~$ set -x
    username@name:~$ if test -f celo.env; then
           . celo.env
     fi
    +test -f celo.env   
    
    # type exit after entering this script to get back to terminal 
    # this argument make sure that the celo.env file will utilize dependencys from the bash script    
         
##   *Use Curl to insert 2 github scripts, these will create 2 new files*
     
    username@name:~$ curl -o celo.env https://gist.githubusercontent.com/alchemydc/ce712f6f3caa7ec79f15f930ed5904ed/raw/385c65b1d3f760854258bfd6dd8cbd135710b78f/celo.env 
    # .env files are for storing and accesing enviromental variables, in this case creating variables for the public keys 
    #  try inserting this curl scrip multiple times if it dosent work the first time 
   ![image](https://user-images.githubusercontent.com/80616939/118896605-d9f96380-b8c5-11eb-883e-18d3d4e94a25.png)
     
    username@name:~$ curl -o start_celo.sh
    https://gist.githubusercontent.com/alchemydc/e28945f5059acd70969b39a50fd0f80a/raw/0d15cceb89ea86ca46df94441c06ecd88a4e6635/start_celo.sh
    # this creates a new file for running the light client and storing node information 
    # it also utilizes docker to make it more efficient 
   ![image](https://user-images.githubusercontent.com/80616939/118896884-7c194b80-b8c6-11eb-9fb8-87be4bcf1246.png)
       
    username@name:~$ curl -o celo.env https://gist.github.com/alchemydc/e28945f5059acd70969b39a50fd0f80a
    # this script allows the Celo client to utilize the celo.env file
    # it also stores important node information 
    # notice the name(celo full-node) this is the name of the node witch is important to remember
    username@name:~$ chmod u+x start_celo.sh
    # chmod u+x grants only the owner of this file execution permissions 
    username@name:~$ pwd
    # pwd prints your current directorys 
  to learn more about these .env files check out this guide:https://learn.figment.io/network-documentation/extra-guides/dotenv-and-.env 
 
##   *To access these files easily move them into a new directory  CALLED; celo-data-dir*
    # the directory will be called celo-data-dir
    username@name:~$ mkdir celo-data-dir
    # mkdir makes a new directory 
    
    username@name:~$ cd
    # use cd by itself to exit any current directorys
    username@name:~$ cd celo-data-dir
    # use cd by itself to exit any current directorys 
    
    username@name:~/ celo-data-dir$ mv ../celo.env .
    # mv moves the file into the directory  
    username@name:~/ celo-data-dir$ mv ../start_celo.sh .
    
    username@name:~$ celo-data-dir$ source celo.env
    # source in both the files so they are up to date 
    username@name:~$ celo-data-dir$ source start_celo.sh
    # now these files can be utilized while in the celo-data-dir 
  ![image](https://user-images.githubusercontent.com/80616939/118896499-9a327c00-b8c5-11eb-8e0d-f9b8c4afec89.png)
   ## *Create the Node, while in the celo-data-dir while*
    
    username@name:~/ celo-data-dir$ sudo usermod -aG docker $YOURNAME
    -Restart the VM
    # after joining a new group always restart the VM. 
    # after making big changes to the VM it is a good idea to restart it 
    
    username@name:~/ celo-data-dir$ id (check if you have joined the docker group)
    username@name:~/ celo-data-dir$ cd celo-data-dir
    # now you are using the celo-data-dir 
    
    username@name:~/ celo-data-dir$ source celo.env
    username@name:~/ celo-data-dir$ cat celo.env
    username@name:~/ celo-data-dir$ docker run -v $PWD:/root/.celo --rm -it $CELO_IMAGE account new (this comand creates your new node)
    # this comand creates your new node account
   Copy the public address key so you can paste it into celo.env using vim
    
    username@name:~/ celo-data-dir$ vim celo.env
 ![image](https://user-images.githubusercontent.com/80616939/118758843-044b1280-b82d-11eb-88df-e83e5fb813f4.png)
 once in vim press i for insert mode
 delete YOUR_ACCOUNT_ADDRESS with Backspace
    
 than paste in the address with shift insert  
    
 exit vim by pressing : than typing wq enter 

    username@name:~/ celo-data-dir$ source celo.env 
    # every time you make a change to celo.env you must source it in
    
  find celo.env in Files under celo-data-dir. Their you can edit it to add more accounts, and address.
  
  remember to source celo.env after making changes.
  
  this is what my celo.env looks like:
  
  ![image](https://user-images.githubusercontent.com/80616939/118337560-498bdf00-b4d1-11eb-8aa3-41857f1079b1.png)
 
  ## *Running the light client*

    username@name:~/ celo-data-dir$ ./start_celo.sh (this is the new comand to start the light client)
    -Create new window in terminal while light client is running to enter the following comands 
    username@name:~/ celo-data-dir$ celocli node:synced 
    # if true your light client is synced with the node and running properly
    
    username@name:~/ celo-data-dir$ celocli account:balance $ACCOUNT
    # this shows the ballance of a given celo account number 
    # where it says $ACCOUNT enter the name that is in celo.env or the public address  
    username@name:~/ celo-data-dir$ docker stop geth
    # this stops the light client  
![image](https://user-images.githubusercontent.com/80616939/118338503-8eb11080-b4d3-11eb-99a3-11417cf79b32.png)

##   *Sending Celo to and from the node*
   
   Send both CUSD, and the CELO native token to your nodes public address 
   
   have 2 terminal windows open one for running the light client and the other for entering comands
   
   make sure both are in the celo-data-dir 
    
    username@name:~/ celo-data-dir$ cat ./start_celo.sh 
    # cat ./start_celo.sh will read you important node information 
    -Find the name of your node, represented in the next command as $NAME
    username@name:~/ celo-data-dir$ docker exec -it $NAME geth attach
    -exit
    username@name:~/ celo-data-dir$ celocli account:unlock $PUBLIC ADDRESS
    -enter password 
    
    username@name:~/ celo-data-dir$ celocli transfer:dollars --from $NODE_ADDRESS --to $PHONE_ADDRESS --value=1e16 (this will send the CUSD from this node to another address         1e16=0.01CUSD) 
    # before you can send CUSD be sure to have CELO native in the account for the gas fee
    # this will send the CUSD from this node to another address 
    # 1e16 = 0.01CUSD, 1e15 = 0.1CUSD, 1e14 = 1.0CUSD
 ![image](https://user-images.githubusercontent.com/80616939/118338915-9c1aca80-b4d4-11eb-87b6-7970949923aa.png)
 
 ##  *Improving the resolution of the VM (Highly recommended)* 
   
    username@name:~$ sudo apt update && sudo apt upgrade
    # this makes sure your system is up to date 
    
    username@name:~$ sudo apt install build-essential linux-headers-`uname -r`
    # these linux headers can be built more than once 
   
    -click "insert vm guest additions CD" under devises in the top left corner
    -cancel 
    
    username@name:~$ cd /media/cdrom
    username@name:~$ sudo sh VBoxLinuxAdditions.run
    # this Linux virtual box program improves the resolution of the VM 
    -Restart the VM 
    # now you can strech the screen from the corners and the resolution will change 
    
## *Conclusion*

After completing all the vital steps the fullnode and light client are ready to go. Keep in mind this tutrial is not the only way to set up a full node. This node is now under your control so remeber the keys and passwords to avoid losing any money. Please experimenting with this setup and personalize it. The start_celo.sh and celo.env files are both easily customizable. If you run into any issues please contact me on Discord; Aidan68#9447. 
#
   **About the Author** 
   
This tutorial was put together by Aidan Dedecker. Here is my git hub page: https://github.com/Aidandedecker/Aidandedecker. here is my figment forum profile: https://community.figment.io/u/aidanbdedecker/summary.
