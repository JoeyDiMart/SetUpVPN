# Set Up a VPN so we can do some home made CTFs
- Okay so homemade CTFs suck because we're on Campus Wi-Fi so here are some docs to show you how to use AWS Lightsail
- Start off and make an AWS account first 

1. Make sure you're on the PAID AWS tier, and first of all go ahead and select Lightsail and "Create Instance"
2. Here's for creating an Instance in Lightsail
- I'm using a Linux/Unix 
- OS only blueprint -> Ubuntu 22.04
- I created a custom SSH key and it'll give you a {given name}.pem file
- run ```chmod 400 {name}.pem```
- in the AWS Lightsail dashboard go to your instance name (I named mine Ubuntu) and select "Networking" and create Static IP
- this Static IP is needed so it doesn't change since we're using this to host a VPN server
- when you ssh into your instance you'll do ```ssh -i /path/to/.pem ubuntu@{static_IP}``` static IP is a public IP btw

3. okay setting up light sail is done ssh into it lets ssh into it so we can set up WireGuard (the VPN we're using)
- just run these commands I'll comment them so you know why 
```shell
sudo apt-get update # start with this
sudo apt-get upgrade
```
```shell
wget https://git.io/wireguard -O wireguard-install.sh && sudo bash wireguard-install.sh  # got this from https://github.com/Nyr/wireguard-install
```

4. Okay it's installed and we're setting up WireGuard now, you should be in a WireGuard window
- it'll ask for IP/Hostname, I didn't change it so leave it blank if you want
- it'll then ask for what port to use, I didn't change it, it's default 51820
- "Enter a name for the first client" I chose "Root"
- DNS — pick 3 for 1.1.1.1

5. go onto the AWS Lightsail console again and check out "Networking" and configure the firewall. Open port 51820
(or whatever port you're using) for UDP traffic

6. take that {first_client}.conf file and send it out to everyone, since my first user is Root my file is Root.conf
- optional but I installed "Magic Wormhole" I use this for file transfer all the time I like it a lot
- run ```sudo apt install magic-wormhole``` if you want it but you also need it on both devices
- I'm sending it to my host machine first then gonna send it to the Root discord

7. To connect, make sure wireguard is installed with ```sudo apt install wireguard -y```
   - then after installing on the clients computer run ```sudo wg-quick up ~/Root```
   - if conf file doesn't exist make sure to move it to /etc/wireguard/{file.conf}
     - if you get an error run ```sudo apt install resolvconf -y```

8. run bash wireguard-install.sh to generate a new conf file for each person 
- I just grabbed everyone's initials so Joey DiMartino I created him a JD.conf


## Note
### Here's what the .conf will look like and the importance 
[Interface]
Address = {subnet_mask_IPv4}, {subnet_mask_IPv6}  # IP would be different this is just mine 
DNS = 1.1.1.1, 1.0.0.1
PrivateKey = something 

[Peer]
PublicKey = Something
PresharedKey = something
AllowedIPs = 0.0.0.0/0, ::/0    # allows for peer-to-peer rather than only client to server, 
meaning you need this since your VM hosting the CTF will be a client as well

Endpoint = {some_IP}:51820
PersistentKeepalive = 25


## Commands for the VPN client

#### Install VPN client on Linux
```shell
sudo apt install wireguard -y
```

## Install VPN client on MacOS
```shell
brew install wireguard-tools
sudo mkdir -p /etc/wireguard
# Note that MacOS gave me issues, I would connect then immediately fall back and disconnect
# had to resort to getting the WireGuard app from appstore
```

#### put config file in correct place
```shell
sudo mv /path/to/conf/file/{conf_file_name} /etc/wireguard 

```

#### Start up the VPN:
```shell
sudo wg-quick up ~/{config_file}
```

#### check if the VPN is running:
```shell
sudo wg show 
```

#### Shut down VPN:
```shell
sudo wg-quick down {config_file}
```

