# Personal-VPN-Server-Setup

# Setting up a VPN  
How it works

Computer -> Regular Internet -> Travel Router -> Raspi/Cloud Service -> Work

What is looks like to others

Rapi/Cloud Location -> Work

This guide uses raspi4

# Enabling VPN Service Using Raspberry Pi 4
Requirements before starting: 
Raspberry Pi (Preferably Pi 4)
### 1) Setting up the PiVPN
1. Run in console```curl -L https://install.pivpn.io | bash```
2. Throughout the setup you will be asked multiple options for setup. Use the following:
     * Interface: eth0 (if using ethernet) OR wlan0 (using wifi)
     * Use DHCP Reservation on Server: (You should set up a static IP for the raspi. If you do now do the following step:
          *    ##### Setting up static IP (Skippable if Done)
               * Find the current raspi IP - Run ```hostname -I```
               * Find the router gateway address - Run ``` ip r | grep default``` and note the first IP given```
                * Find the current DNS setup ``` sudo nano /etc/resolv.conf``` and note the address after name server
                * Once you have noted all of these it's time to edit dhcpcd.conf ```sudo nano /etc/dhcpcd.conf```
                 * Add the following lines if they don't exit or comment out the previous lines and add this:
                 ```
                interface NETWORK static 
                ip_address=STATIC_IP/24
                static routers=ROUTER_IP
                static domain_name_servers=DNS_IP
                ```
                * Replace NETWORK – your network connection type: eth0 (Ethernet) or wlan0 (wireless).
                * Replace STATIC_IP – the static IP address you want to set for the Raspberry Pi.
                * Replace ROUTER_IP – the gateway IP address for your router on the local network.
                * Replace DNS_IP – the DNS IP address (typically the same as your router’s gateway address).
                * Here is an example configuration to set the static IP to 192.168.1.120 with a wireless connection to a router at 192.168.1.254:
                    ```
                    interface wlan0
                    static ip_address=192.168.1.120/24
                    static routers=192.168.1.254
                    static domain_name_servers=192.168.1.254
                    ```
                * Reboot the raspi using ``` sudo reboot```
                * To check that it is working correctly, enter the following command: ```hostname -I```
                * You should now see the static IP address that you set in the dhcpcd.conf configuration file.
                * #### Set a Static IP Address: Success
                * Congratulations: you have set up a static IP address on your Raspberry Pi and it should now retain that address automatically whenever it boots up. Now you can go ahead and use your Raspberry Pi as a NAS, media or game server, and connect to it reliably at the same address every time.
                * #### Port Forward the IP on your router (may not be necessary)
                * There are various names and UI's for routing. You should attempt to do the following configurations:
                    ```
                    Enable Port Fowarding
                    Triggering Port: 1776
                    External Port: 17776
                    External Protocol: TCP & UDP / ALL
                    
                    Enable public access to your port
                    Virtual Servers:
                    External Port: 1776
                    Internal IP:  (static raspberry pi IP)
                    Internal Port: 1776
                    Protocol: TCP & UDP / ALL
                    ```
    ### Continue from here if you skipped setting up static ip            
    * Local User: pi (or you can choose your own)
    * Choose a VPN: OpenVpn
    * Customize Settings: Yes
    * Modify the default port 1776
    * DNS Provider: Google
    * Public IP or DNS:
        * Custom Search Domain
                * Yes (Use DDNS FREE)
                * No( It will auto create one for you)
        * Use public IP (Should be the IP of your house - ONLY USE THIS IF YOUR IP IS STATIC)
        * DNS Entry
            * What is the public DNS Name of the server?
    * Installation Mode - OpenVPN 2.4: Yes
    * ECDSA certificate Size: 256
    * Unattended Upgrades: Yes
    * Reboot: Yes


### 2) Setup PiVPN Profile
Setting up the PiVPN is as simple as running the followinc code

    pivpn add
    Enter a name for the Client: full_tunnel (can be whatever u want )
    How many days should the cetficiate last: 1080 (can be whatever you want )
    Enter password to verify:  ********** (Enter some password)

* #### Enabling High Speed
    For good speed  requires a value called the MSS to be set. The MSS is the value for the MTU minus 40). 
    1) ##### Find the proper METU VALUE
        * On Windows ```ping -n 1 -l 1500 -f www.example.com ```
        * On Linux ```ping -M do -s 1500 -c 1 www.example.com```
        * On OSX ```ping -D -v -s 1500 -c 1 www.example.com```
        Decrease the 1500 value by 10 each time, until the ping succeeds. Once the ping succeeds, **the value used is the MTU you should use**.

    2)    ##### Setting the proper MTU Value
            * Note the value used to allow ping to be successful. Subtract this by 40 to get your MSS.
            * Eg. If your MTU is 1460, your MSS is 1420
                MSS = MTU  - 40
                MSS = 1460 - 40
                MSS = 1420
    3) ##### Provide the MSSFIX to the .ovpns profile
        * Edit your .ovpns file with any text editor.
        * Add ```mssfix (MTU Here)``` to a new line at the top.
### 3) Downloading the .ovpns file
* We need the .ovpn file that was generated from out pi. 
* We have a few options:
    1) If using the pi GUI you can email it (not recommended)
    2) If ssh is enabled 
        a) ssh into your pi to ensure ssh connection works
        b) ```scp pi@192.168.0.112:/home/pi/ovpns/raspi.ovpn ~/Desktop```
    3) Plug a USB into the pi,transfer the file, and plug it into your computer and save it

### 4) Import the OpenVPS file 

- [Related Videos](https://www.youtube.com/watch?v=nKnYLQplovI&list=PLYl5sY0sL98hJRpne6ShX1I9JJ6MVIH4q&index=4) - DDNS not required
- [Related Video 2](https://www.youtube.com/watch?v=kLmbgJe1rEU)
- 

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








