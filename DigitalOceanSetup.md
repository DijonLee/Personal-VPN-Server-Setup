# Personal-VPN-Server-Setup

---
# Setting up VPN Using Digital Ocean
### Requirements before starting: 
[Sign up for digital ocean](https://m.do.co/c/8e844a776b79)

[Buy a travel router with VPN](https://www.gl-inet.com/products/gl-mt1300/)

## Create a droplet (virtual private server)
### 1) Choose an image 
* Cick marketplace, and search for OPENVPN 
* Select the one indicating **OpenVPN Access Server**
Enabling VPN Service Using Digital

### 2) Choose a plan
* Select a Basic Plan
* Select $5 a month
* _You most likely won't need more than a $15 a month plan. Start off with a $5 plan and work from there_
* Choose the datacenter region you would like to be seen as

### 3) Authentication
* Select SSH Keys/ Add new Key 
* On Mac
    * ``` ssh keygen```
    * You will be prompted to save and name the key.
    *       Generating public/private rsa key pair. Enter file in which to save the key (/Users/USER/.ssh/id_rsa):
    *       Next you will be asked to create and confirm a passphrase for the key (highly recommended):
    * Enter passphrase (empty for no passphrase):
    * Enter same passphrase again: 
    This will generate two files, by default called id_rsa and id_rsa.pub. Next, add this public key.
* On Windows? Use Putty
* Add the public key
* Copy and paste the contents of the .pub file, typically id_rsa.pub, into the SSH key content field on the left of digital ocean including the ssh-rsa using  ```cat ~/.ssh/id_rsa.pub```
    * Example:
    *     ssh-rsa AAAA333333333333.....asdasdsadc=

### How many Droplets?
You only need one droplet but it is a good idea to have a backup datacenter nearby like NYC  (USEAST) and a backup datacenter California (USWEST)

### SSH into your server
1) Get the IP Address of your droplet
2) ssh into your droplet
On mac 
    * ```ssh root@xxx.xxx.xxx.xx```
    

* Will this be the primary access Server Node? **Press Enter (use default)**
* Specify the network interface and IP address to be used by the Admin Web UI? **Press Enter (use default)**
Enter the port number for the Admin Web UI? **Press Enter (use default)**
* Please specify the TCP port nuumber for the OPENVPN daemon? **Press Enter (use default)**
* Should client traffic be routed by defauly be routed through the VPN?  **Press Enter (use default)**
* Should client DNS traffic be routed by default therough the VPN? **Press Enter (use default)**
* Use local authentication via internal db? **Press Enter (use default)**
* Should private subnets be accessible to clients by default?  **Press Enter (use default)**
*  Do you with to login to the ADMIN UI as "openvpn"?  **Press Enter (use default)**
* Please specify your Activation key or leave blank?  **Press Enter (use default)**
    

You can now continue to configure OpenVPN Access Server by directing your web browser to this URL ```droplet_ip_address:943/admin``` but before you do so.. Set up a password
```
passwd openvpn
Enter new UNIX password: enter_some_password
Retype new UNIX password: enter_some_password
passwd: Password updated sucessfully
```
* If you are unable to access on chrome try a different browser*

Go to ```droplet_ip_address:943/admin``` and login using ```username: openvpn``` and password ``` enter_some_password_you_setup``` and Agree to the ToS. If they harass you do download the app. log out and log in or try to bpyass it.

## Download the .openvpn file 
Go to ```droplet_ip_address:943```  (user client) 
L og in using the credentials you specified above

Scroll down to ```Available Connection Profiles:``` 
Click ```Yourself (user-locked profile)``` to download your xxx.ovpn - I would name it something like OpenVPN- US-EAST-1

## Setup OpenVPN on your travel router

This will direct all traffic from your router to hit the OpenVPN instance prior to hitting any other endpoint. That is... Computer -> OpenVPN -> Travel Router ->  Wherever you want to go.
We will be using the beryl router for this tutorial

1. Power on your travel router using the usb-c cord
2. You can connect to the router via Ethernet cable or Wi-Fi. The default password is *goodlife*
3. Access the web Admin Panel
    * Open a web browser (we recommend Chrome, firefox) and visit http://192.168.8.1. You will be directed to the initial setup of the web Admin Panel.
4. Setup Language and Password Settings
5. From here you can now setup your router to enable OpenVPN.
6. Connect your Travel Router to the Internet either via Wifi or Ethernet. If you would like to connect it using Wifi you need to enable it as a Repeater and connect to your designated wifi network
7. Access to web Admin Panel, on the left side -> VPN -> OpenVPN Client
8. Add new OpenVpn Configuration file and upload the ovpn file you saved earlier
    * Description: OpenVPN-US-East-1 (or whatever you want)
    * User Name: openvpn
    * Password: enter_some_password (Whatever you set password to up top)
9. **Enable the Kill Switch** For the love of god and all things holy. Make sure this is enabled. 
110. On the left side of web Admin Panel -> VPN -> Internet Kill Switch
11. Go Back to OpenVPN and press connect
12. Ensure the following:
    * You are only connected to the router
    * Go here https://ipleak.net/  and verify the VPN address says your VPN location
    * The Kill switch is enabled
    * I **highly** recommend you disable wifi on your computer and only connect to your travel router via ethernet.  Best way to this? Power the router using your laptop
 
Thats it!

There are a few things I would do from here.
1) Try out this setup at home using a home country openvpn for a few weeks.


https://www.youtube.com/watch?v=Bc8Nriy8_cE
https://docs.gl-inet.com/en/3/tutorials/openvpn_client/
https://docs.gl-inet.com/en/3/setup/gl-mt1300/first_time_setup/
https://docs.gl-inet.com/en/3/tutorials/connect_to_a_hotspot_with_captive_portal/
https://docs.gl-inet.com/en/3/tutorials/internet_kill_switch/








