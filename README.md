# Raspberry-Pi-Setup
Steps to Set Up my Raspberry Pi

## Logging in and setting up Config

___1. Login to Raspberry Pi___

Enter the following command to login to the raspberry pi:

```
ssh pi@raspberrypi.local
```
When asked to add to the list of known hosts type

```
yes
```

When prompted the default password is:

```
raspberry
```

___2. Change the configurations___

Enter the following command to enter configurations

```
sudo raspi-config
```

First change the user password under 1.

Now change the hostname under 2 -> N1.

I changed to college-hole.

Now allow wifi access under 2 -> N2

Change boot options under 3 -> B1 -> B2

Change allow network on boot under 3 -> B2 -> No

Allow ssh access on 5 -> P2 -> yes

Lower memory under 7 -> A3 -> 16

Now finish and reboot!

__3. SSH without password__

Type in the following command to make it so you no longer have to use a password

```
ssh-copy-id pi@college-hole.local
```

___4. Update the Pi___

ssh back into the Pi using the following command:

```
ssh pi@college-hole.local
```

Now type the following command to update the pi

```
sudo apt update && sudo apt -y upgrade
```

Finally reboot the PI using:
```
sudo reboot
```

___5. Setting Static IP (Optional)___

Run:

```
ip -4 addr show | grep global
```

The first address is the IP address of your Pi on the network, and the part after the slash is the network size. It is highly likely that yours will be a /24.

The second address is the brd (broadcast) address of the network.

Find the address of your router (or gateway)

```
ip route | grep default | awk '{print $3}'
```

Finally note down the address of your DNS server, which is often the same as your gateway.

```
cat /etc/resolv.conf
```

Edit /etc/dhcpcd.conf as follows:-

```
interface eth0
       static ip_address=10.1.1.30/24
       static routers=10.1.1.1
       static domain_name_servers=10.1.1.1

interface wlan0
       static ip_address=10.1.1.31/24
       static routers=10.1.1.1
       static domain_name_servers=10.1.1.1
```  

Then disable the DHCP client daemon and switch to standard Debian networking:

```
sudo systemctl disable dhcpcd
sudo systemctl enable networking
```

Now reboot the system for the change to take effect

```
sudo reboot
```

## Installing OpenVPN

Now it's time to set up openvpn. Execute the following commands:

```
wget https://git.io/vpn -O openvpn-install.sh
chmod 755 openvpn-install.sh
sudo ./openvpn-install.sh
```

Accept all the defaults and wait a while.

## Install Pi-Hole

Now it's time to get Pi-Hole onto the pi.

Run the command:
```
curl -sSL https://install.pi-hole.net | bash
```
Choose all the defaults except choose ___tun0___ as the interface.
*Note if it stops halfway through, the download failed and you have to restart the pi.


After finishing I usually change/remove the password for the pihole by running:

```
pihole -a -p
```

## Configure OpenVPN

First get the IP address of the tun0 interface. Run:

```
ifconfig tun0 | grep 'inet'
```

For my pi it was 10.8.0.1

Now we want to edit the openvpn config file. Run:

```
sudo nano /etc/openvpn/server.conf
```

Now find all the options with
```
push "dhcp-option DNS <some number here>"
```
Add a ___#___ to the beginning of their lines

Add the following line with your tun0 ip address:
```
push "dhcp-option DNS 10.8.0.1"
```

Now exit and save the file. Restart the OpenVPN with:
```
sudo systemctl restart openvpn
```

## Creating and Copying Users

To create a new Open VPN user run:
```
sudo ./openvpn-install.sh
```

Change the permissions of the root files by running:
```
sudo chmod 755 /root/
```


Now copy the user to your local device by running (outside the pi)

```
scp pi@college-hole.local:/root/<USER>.ovpn .
```

## Firewalls

Run the following command to allow traffic on the tun0 interface:

```
sudo iptables -I INPUT -i tun0 -j ACCEPT
```
