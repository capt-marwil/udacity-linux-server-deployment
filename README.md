## Project Deploying to a Linux Server ###

### Created private and public ssh-key pair on my machine ###
- created a public key `grader` with `ssh-keygen`
- set passphrase for public key


## Set up an AWS Lightsail Instance ##
- Chose Instance Location Frankfurt, Zone A (eu-central-1a)
- Selected Linux/Unix platform and OS Only as a blueprint
- Changed the SSH key pair and uploade the previously created public key for grader
- changed IP Adress to statc IP address 18.194.127.232

## Accessing the remote machine ## 
- logged into the remote machince with `ssh -i ~/.ssh/<NAME OF PUBLIC KEY FILE> grader@18.194.127.232`

## Checking and updating repos ##
### Listed the availabel repos ###
`cat /ect/apt/sources.list `
### Made sure the system knows repos ###
`sudo apt-get update`
### Updated the System ###
` sudo apt-get upgrade`
### removed packages that are not needed anymore ###
`sudo apt-get autoremove`
### Installed packages needed for my web app ###
`sudo apt-get install apache2`
`sudo apt-get install postgresql` 
`sudo apt-get install memcache`

## Configuring Users ##
### Installed finger ###
`sudo apt-get install finger`

### Created an new user grader ###
`sudo adduser grader`

### Added user grader to sudoers ###
`sudo cp /etc/sudoers.d/90-cloud-init-users cp /etc/sudoers.d/grader`
`sudo nano /etc/sudoers.d/grader`
Changed `#User rules for ubuntu` to `grader ALL=(ALL) NOPASSWD:ALL`

## Enabled key based Access ##
### Created a SSH Directory on remote server ###
- in grader home directory `~$` `mkdir .ssh`

### Created a keyfile on remote server ###
`touch .ssh/authorized_keys`

### Copied the content of the public key file from the local machine to remote server using nano ###
`nano ~/.ssh/authorized_keys`
### Set special permissions on .ssh and authorized_keys
`chmod 700 .ssh`
`chmod 644 .ssh/authorized_keys`

### Disabled Password base login on remote server ###
- Edited /etc/ssh/sshd_config `sudo nano /ect/ssh/sshd_config`
- Changed Option `PasswordAuthentication` from `yes` to `no` (already active on Lightsail Instance by default)
- restarted ssh service `sudo service ssh restart`

## Securing the server ##
### Installed the firewall ###
`sudo apt-get install ufw`

### Explored the firewall status ###
`sudo ufw status` or `sudo ufw status verbose`

### Turned the firewall on and off ###
`sudo ufw enable`
`sudo ufw disable`

### Added rules to the firewall ###
* As a default denied all incoming traffic setting `sudo ufw default deny incoming` 
* As a default allowed all outgoing traffik by setting `sudo ufw default allow outgoing` 
* Configured a non standard port for ssh `sudo ufw allow 2200/tcp`
* Allowed http connections on port 80 `sudo ufw allow www`
* Allowed ntp connections on port 123 `sudo ufw allow ntp`
* Deleted unwanted rules `sudo ufw delete allow <NAME_OF_SERVICE>`

## Deploying my CatalogApp to the server ##

* Mloned my catalog repo to the server with `git clone https://github.com/capt-marwil/Udacity-Item-Catalog.git``
* Made sure that all the required python modules as describe in my project README were installed https://github.com/capt-marwil/Udacity-Item-Catalog
* Set up a directory structure, virtual env and files as described in this guide by Digital Ocean https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
* Ending up with a project structure like this:
- |-/var/www/Catalog/
- |-----------------Catalog/
- |-----------------catalog.wsgi
- |-----------------------------__init__.py
- |-----------------------------client_secrets.json
- |-----------------------------fb_client_secret.json
- |-----------------------------populate_database.py
- |-----------------------------setup_database.py
- |-----------------------------static/
- |-----------------------------templates/
- |-----------------------------venv/

* directories /var/www/Catalog/Catalog/static and /var/www/Catalog/Catalog/templates containing css files, javascrict, img, template files etc.
* Changed the SQL Alchemy Engine object in __init__.py, populate_database.py and setup_database.py from sqlite to postgresql
* Restarted the webserver with `sudo service apache2 restart`
* The Catalog App can be reached at http://18.194.127.232/

## Problems left to solve ##
* It's currently not possible to login with Google or Facebook as both API don't recognize the new ip address and I can't figure out how to change the params
in the json strings for javascript origin and redirect uri







