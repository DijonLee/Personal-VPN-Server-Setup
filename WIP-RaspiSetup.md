# Personal-VPN-Server-Setup

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
