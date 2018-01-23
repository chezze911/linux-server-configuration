# linux-server-configuration
Udacity FSND project

Configuration Steps:
Step 1 : Launch your Virtual Machine with your Udacity account
Development Environment Information Details:-
Public IP Address - 54.218.76.136
Private Key - Can't be shared

Step-2: Follow the instructions provided to SSH into your server
mv ~/Downloads/udacity_key.rsa ~/.ssh/
chmod 600 ~/.ssh/udacity_key.rsa
ssh -i ~/.ssh/udacity_key.rsa ubuntu@54.218.76.136

Step-3 : Create a new user named grader
sudo adduser grader
To check the User(grader) information :
    sudo apt-get install finger
    finger grader
It will give you additional information(login , name , shell, directory, phone number etc) of User-grader

Step-4 : Give the grader the permission to sudo
sudo visudo (edit the sudoers file . To save, use sudo visudo to edit the sudoers file otherwise file will not be saved)
add the below line of code after root ALL=(ALL:ALL) ALL grader ALL=(ALL:ALL) ALL and save it (ctrl-X , then Y and Enter)
Your new user(grader) is able to execute commands with administrative privileges. ( for example - sudo anycommand)
You can check the grader entry by below command: sudo cat /etc/sudoers


Step-5 : Update all currently installed packages
sudo apt-get update - command will update list of packages and their versions on your machine.
sudo apt-get upgrade - command will install the packages
SELECT Keep the local version currently installed and hit ENTER


Step-6 : Change the SSH port from 22 to 2200
ubuntu@54.218.76.136:~$ sudo nano /etc/ssh/sshd_config and enter Yes to confirm editing by root
change port from 22 to 2200
change PermitRootLogin prohibit-password to PermitRootLogin no . It will disable root login.
change PasswordAuthentication from no to yes.
add AllowUsers grader at end of the file so that we will login through grader and save it (ctrl-X , then Y and Enter)
confirm editing was saved by checking the file with sudo cat /etc/ssh/sshd_config
restart the SSH service : sudo service ssh restart

Step-7 : SSH- Keys
generate key-pair with ssh-keygen
Save keygen file into (/home/ubuntu/.ssh/linux_security_configuration_project) and fill in the password. 2 keys will be generated, public key (linux_security_configuration_project.pub) and identification key(linux_security_configuration_project).
Login into grader account using ssh -v grader@"public_IP_address" -p 2200. type the password that you have filled in during user creation (sudo adduser grader step 3) . ubuntu@54.218.76.136:~$ ssh -v grader@54.218.76.136 -p 2200 grader@ip-54.218.76.136 password :
if the password is correct , you will login as grader account: grader@ip-54.218.76.136:~$
make a directory in grader account : mkdir .ssh
make a authorized_keys file using touch .ssh/authorized_keys
from your local machine,copy the contents of public key(linuxProject.pub).
paste that contents on authorized_keys of grader account using nano authorized_keys and save it .
give the permissions : chmod 700 .ssh and chmod 644 .ssh/authorized_keys.
do nano /etc/ssh/sshd_config , change PasswordAuthentication to no .
sudo service ssh restart.
ssh grader@54.218.76.136 -p 2200 -i ~/.ssh/linux_security_configuration_project in new terminal .A pop-up window will open for authentication. just fill the password that you have fill during ssh-keygen creation.







