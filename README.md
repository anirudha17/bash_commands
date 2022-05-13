# bash_commands
//node version update
nvm install 16.14.2 

//node version update for linux
npm install -g n   # Install n globally
n 16.14.2          # Install and use v16.14.2
*******************************************************************************************

//Create React App
npx create-react-app my-app
cd my-app
npm start
npm run build

//Create React App in current directory
npx create-react-app .

//to use npm instead of yarn
delete yarn.lock file and run the command 'npm i'
**********************************************************************************************

//creating git locally
'git init'   //Initialize git - 'ls -a' command will show hidden files 
'git add' <filename> or 'git add .'   //to stage files - 'git status' to check stagging area
'git commit -m "Commit message"'   //to commit files - 'git log' will show the commits committed

'git checkout <commmit hash code>'  //it is used to switch between branches in a repository - 'git checkout master' to go back to master 


//push to an existing repository from the command line
first create a new github repository from github.com
git remote add origin https://github.com/anirudha17/trial.git 
git branch -M main
git push -u origin main --force


//first git add .
//second git commit -m "commit message"
//third git push


// Running git config globally
git config --global user.email "my@emailaddress.com"
git config --global user.name "my_name"
git config –list  //to check the username and email

//Cloning a repository in github - get code from github to local system 
git clone <repo url>
**************************************************************************************************

//Host Websites on a vps//

1.create a vps and connect to that vps    //using 'ssh root@ip_address'

2.sudo apt install apache2

3.sudo ufw enable    //enables ubuntu firewall
sudo ufw status    //checks status of the firewall
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
sudo lsof -i -P -n | grep LISTEN    //check open ports

4.buy a domain from my.freenom.com
go to digitalocean > networking > Domains: and add a domain (example:mywebsite.cf)
that will take you to Domains/mywebsite.cf   Create a new record=>
add two A records > first:@(root)   second:www 
add 3 nameservers in my.freenom.com.   ns1.digitalocean.com,ns2.digitalocean.com,ns3.digitalocean.com

5.mkdir -p /var/www/mywebsite.com/     //Creating Directories for individual sites
Transfer build files related to the website using Filezilla

6.vim /etc/apache2/sites-available/mywebsite.com.conf     //Creating the VirtualHost files
<VirtualHost *:80>
    ServerName mywebsite.com
    ServerAlias www.mywebsite.com
    ServerAdmin yourPublicEmail@email.com
    DocumentRoot /var/www/mywebsite.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

7.cd /etc/apache2/sites-available/    //Enable the VirtualHosts
sudo a2ensite mywebsite.com.conf
sudo service apache2 restart

8.ssl certificate
sudo apt install certbot python3-certbot-apache
sudo ufw allow 'Apache Full'
sudo certbot --apache
sudo systemctl status certbot.timer    //test the auto-renewal of your SSL certificate by firing the following commands
sudo certbot renew --dry-run

9.when going to /about and reloading the page you were getting 404 error
sudo a2enmod rewrite
vim /etc/apache2/apache2.conf
<Directory "/var/www/PATH_TO_YOUR_ROOT">     //edit PATH_TO_YOUR_ROOT
    RewriteEngine on
    # Don't rewrite files or directories
    RewriteCond %{REQUEST_FILENAME} -f [OR]
    RewriteCond %{REQUEST_FILENAME} -d
    RewriteRule ^ - [L]
    # Rewrite everything else to index.html to allow html5 state links
    RewriteRule ^ index.html [L]
</Directory>
sudo service apache2 restart

********************************************************************************************************

//mongodb database on vps//

//Install MongoDB
create a vps
wget -qO - https://www.mongodb.org/static/pgp/server-5.0.asc | sudo apt-key add -  
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/5.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-5.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
sudo systemctl status mongod
ufw allow 27017
mongosh

//create an admin and an user
mongo
use admin

1. Create an admin
db.createUser({user: "admin", pwd: "adminpassword", roles: [{role: "root", db: "admin"}]})

2. Create an normal user with read write permission
db.createUser({user: "user", pwd: "userpassword", roles: [{role: "readWriteAnyDatabase", db: "admin"}]})

//BindIP For Remote Connection & Enable authentication
nano /etc/mongod.conf

Edit the configuration file like this:
net:
  port: 27017
  bindIp: 0.0.0.0
security:
  authorization: enabled

Restart the server:
systemctl restart mongod

//Connect
1.mongodb compass-connection string
mongodb://<username>:<userpassword>@<ip-address>:27017

2.mongo shell
exit the mongo shell then type
mongo admin -u uname -p 'password'

//check the user which is logged in
db.runCommand({connectionStatus : 1})

//In order to list all users in the Mongo shell
db.getUsers()

//MongoDB loads but breaks, returning status=14
sudo chown -R mongodb:mongodb /var/lib/mongodb
sudo chown mongodb:mongodb /tmp/mongodb-27017.sock
sudo service mongod restart
************************************************************************************************************************
//VPN//

1.create a vps

2.run commands
apt update
apt upgrade
apt install ca-certificates wget net-tools gnupg
wget -qO - https://as-repository.openvpn.net/as-repo-public.gpg | apt-key add -
echo "deb http://as-repository.openvpn.net/as/debian focal main">/etc/apt/sources.list.d/openvpn-as-repo.list
apt update
apt install openvpn-as

3.Access the Admin Dashboard
after 'apt install openvpn-as', you will get 'https://<your-ip>:943/admin' on your terminal. username and password also come in terminal
go to https://<your-ip>:943/admin and login
go to user management - user permissions and add a new user , allow that user auto login and save settings
go to more settings and set password for that user
go to https://<your-ip>:943/ and login with user
go to profiles management and click on create and you will download a .ovpn file

4.'openvpn connect' application in windows and drag and drop .ovpn file in it.

*****************************************************************************************************************************

//windows 10 remote desktop//

1.Create 2 vps with 8gb ram. Name 1st vps as 'image-server' and 2nd as 'windows'

2.Download windows 10 iso in your pc.
  a.Download windows 10 installation media from microsoft website.
  b.Run it and important options to select are  
    - create installation media
    - iso file

3.Open FileZilla and drag and drop the iso file into your image-server vps. 

4.ssh into your image-server and run the commands
   apt-get update && apt-get install qemu -y
   qemu-img create -f raw windows10.img 30G
   wget https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso

5.Copy and paste the following lines and press enter twice(so there are no arrows)
qemu-system-x86_64 \
  -m 6G \
  -cpu host \
  -enable-kvm \
  -boot order=d \
  -drive file=Windows.iso,media=cdrom \
  -drive file=windows10.img,format=raw,if=virtio \
  -drive file=virtio-win.iso,media=cdrom \
  -vnc :0 \

6.Now open vnc viewer and connect to your image-server vps

7.Select your language. If you don't have a product key, select i don't have a product key and continue. Select windows 10 pro instead of home.

8.Select Custom: Install Windows only (advanced).

  Then select Load driver then click browse.
  You’ll want to scroll down to CD Drive (E:) virtio-win-0.1.1.
  Now we can select the Network driver, which can be found in E:\NetKvm\w10\amd64.
  We will also need to uncheck Hide drivers that aren't compatible with this computer's hardware then select the first option Red Hat VirtIO Ethernet Adapter and next.

  Now we need to repeat this process again selecting Load driver, browse and scrolling down to CD Drive (E:) virtio-win-0.1.1.
  This time we need to select E:\viostor\w10\amd64 and install the Red Hat VirtIO SCSI controller.

  We should now see our drive. Go ahead and highlight it then click next

  Now we wait for the installation to complete.

  If it shows a error message that something went wrong then select skip.
  
  Sign-in with your microsoft account

  After completing the installation(once you can see windows10 homepage) close the vnc viewer and enter ctr+c in console

9. Compress the image using the following command.
dd if=windows10.img | gzip -c > windows10.gz

This will take a long time, The following will show up when it’s done.
33554432+0 records in
33554432+0 records out
17179869184 bytes (32 GB, 30 GiB) copied, 723.762 s, 23.7 MB/s

Once the image has been compressed we can go ahead and host it using the following.
python3 -m http.server

10. Go to the DigitalOcean dashboard and open the windows droplet we created earlier.
First click Recovery, then select Boot from Recovery ISO and turn off the droplet and turn it back on
Once it’s powered on with the recovery ISO select console in upper right
press 6 for shell

paste the following command after replacing imageserver with the IP of your imageserver droplet.
wget -O- http://imageserver:8000/windows10.gz | gunzip | dd of=/dev/vda

It will take a long time. please keep patience

Once the copy is complete we can run shutdown 0 and go back to the recovery page on the digital ocean dashboard. 

Then select Boot from Hard Drive and power the droplet back on and press the console button.

You will get an alert 'Update Droplet Console'. Select 'Launch recovery console'.

11. Network settings
Click on the network icon in the bottom right, then selecting Network & Internet Settings
Click Change adapter options at the bottom of this window.
Select our Ethernet adapter and click the little drop down in the top right, then selecting 'Change settings of this connection'
Now double click on 'Internet Protocol Version 4 (TCP/IPv4)' to bring up the IPv4 settings.

You can use the information at the bottom of the Droplet Console to complete the next section. Ip address, Subnet mask, Default gateway.
You will also need to enter DNS servers. use 1.1.1.1 provided by CloudFlare and 8.8.8.8 provided by Google.

12. Remote Desktop
First we goto the start menu and search 'Remote Desktop settings'.
From here we toggle the 'Enable Remote Desktop' button, then click 'Advanced settings'.
Lastly we need to uncheck 'Require computers use Network Level Authentication'.

that's it, now you can open 'Remote Desktop Connection' on your pc and connect to the vps

**************************************************************************************************************************************





