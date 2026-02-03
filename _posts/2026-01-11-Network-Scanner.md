---
layout: post
title: "Internet Device Scanner"
date: "2026-01-11"
--- 
## Materials Used:

- [Raspberry Pi 1 (Model B 2012)](https://docs.rs-online.com/6403/0900766b8127da4b.pdf)
- Memory Card
- Power source
- Ethernet Cable
- Laptop
- USB Drive

## Purpose

The RPi scans who's on the internet and logs it. It logs when devices connect and disconnect, allowing for an overall watch on your internet.

## Process

In my previous entry, I created a local drive using a usb drive connected to the RPi. In this project, I am going to be using that to store my log.

```bash
sudo apt install fping -y
sudo apt install arping -y
sudo setcap cap_net_raw+ep /usr/bin/fping
```
We begin by installing both fping and arping libraries. Fping allows for quick, easy IP scanning while arping tests for host reachability. The setcap command allows for any user to run the fping command and have it work properly.

Now let's open up our file where we will be writing our code.
```bash
nano /home/(name)/network_scanner.py
```
Let's now set up our libraries and some variables.

![Here's-our-code]({{ site.baseurl }}/images/usbpi3.png)

Ensure that the ROUTER_IP matches actual router IP as well as that LOG_FILE and SCAN_INTERVAL are set properly. Here I am using a text file in my usb drive to store the log. You can add as many known devices as you want.

![Here's-our-code]({{ site.baseurl }}/images/usbpi4.png)

Define the log function. I have modified it so that when ran in the console, it makes the output easier to read however in the actual text file, all information is still written out.

![Here's-our-code]({{ site.baseurl }}/images/usbpi5.png)

Define the fping function. This function allows for the network scan to figure out who is on the internet.

![Here's-our-code]({{ site.baseurl }}/images/usbpi6.png)

Define the arping function. This function allows for the pi to remember a device that is on the network.

![Here's-our-code]({{ site.baseurl }}/images/usbpi7.png)

Define the get-mac-address function.

![Here's-our-code]({{ site.baseurl }}/images/usbpi8.png)

Define both device type and friendly name functions.

All that is left is to define the main loop. 

![Here's-our-code]({{ site.baseurl }}/images/usbpi9.png)
![Here's-our-code]({{ site.baseurl }}/images/usbpi10.png)

Next allow for the code to be executable.

```bash
chmod +x /home/malcolm/network_monitor.py
```
Allow for autorun on boot by modifying and copying the following:
  
@reboot sleep 30 && /usr/bin/python3 /home/(Name)/network_monitor.py >>

Then paste it into the cron table at the bottom by entering
```bash
crontab -e
```

The process starts on bootup of the RPi naturally but can be executed directly.
```bash
python3 name_of_file.py
```

## Product

Here's the view from the console. We see a simple view indicating what connects, what's online, and we can see that the script exits gracefully.

![Here's-our-product]({{ site.baseurl }}/images/usbpiel.png)

Here's the view in monitor.log. We see an expanded version and see the local IP addresses of devices that aren't recorded in our known devices catalog.

![Here's-our-product]({{ site.baseurl }}/images/usbpi12.png)

We are also able to see when devices connect to the internet and are found.

![Here's-our-product]({{ site.baseurl }}/images/usbpi13.png)

Finally, devices that have disconnected (been offline since grace period began) are reported.

![Here's-our-product]({{ site.baseurl }}/images/usbpi14.png)

## Skills Used

- Linux System Administration
- Hardware-Software Integration
- Problem-Solving & Debugging
- Network Monitoring & Scanning
- Python Scripting & Automation