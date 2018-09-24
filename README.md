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


