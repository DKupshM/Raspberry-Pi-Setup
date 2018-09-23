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




