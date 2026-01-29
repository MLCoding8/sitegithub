---
layout: post
title: "Air Monitor"
date: "2026-01-16"
--- 
## Materials Used:

- [Raspberry Pi 1 (Model B 2012)](https://docs.rs-online.com/6403/0900766b8127da4b.pdf)
- Memory Card
- Power source
- Ethernet Cable
- Laptop
- USB Drive
- PMSA003I
- 8-position, 1.25T cable
- 1.25T to 2.54 converter
- x4 Female-to-female jumper cables
- Multimeter
- Lighter (testing)

## Purpose

The PMS sends information about the air quality to our RPi and our RPi interprets, logs, and displays this information. This device will be useful for ensuring living conditions are suitable. 

## Process

![PMS&Cord]({{ site.baseurl }}/images/aq1.png)

Attach the cable to the PMS, then attach the 1.25T to 2.54 convertor to the cable. 

![Here's our code]({{ site.baseurl }}/images/aq2.png)

Before proceeding, ensure that the RPi does **not** have a power source.

On the converter, add a jumper cable to pins VCC, GND, RXD, and TXD. VCC carries our positive supply voltage, GND will be connected to ground, and RXD and TXD are for receiving and transmitting data respectively. Attach the cables to the GPIO pins of the RPI in accordance with the layout below. Note that the cable attached to RXD on the convertor should be attached to the TXD pin on the RPi and viceversa.

![Layout]({{ site.baseurl }}/images/aq3.png)

![Result]({{ site.baseurl }}/images/aq4.png)

Ensure once more that the pins are properly connected. The device is fragile and incorrect wiring can cause permanent damage. 

Plug in the power source now and give it a second. SSH into the RPi, enter:

```bash
sudo raspi-config
```

On the follow screen click 

![Result]({{ site.baseurl }}/images/aq5.png)

Next click Serial Port.

![Click]({{ site.baseurl }}/images/aq6.png)

For the next prompts, opt to not have a login shell, and then select yes to enable the serial port hardware. Now select click OK then Finish.

```bash
sudo nano /boot/firmware/config.txt
```

Add to the bottom:

```text
enable_uart=1
dtoverlay=disable-bt
```

Reboot to update.

```bash
sudo reboot
```

Ensure that the PMS is powered on. Listen to the box and you will hear a gentle fan. If no fan can be heard, ensure that there is the expected voltage drop from VCC to GND. I used a multimeter, set to 20V, and touched the exposed square sections on the end closest to the converter.

![multimeter]({{ site.baseurl }}/images/aq7.png)

Check if our RPi is receiving data. If none is received, you might need to change the rate. In my case, my unit used 9600 baud.

```bash
sudo timeout 15 cat /dev/serial0 | hexdump -C
```

For me, I did not get any information from this so I did the following.

```bash
stty -F  /dev/serial0 9600 raw -echo
```

This changes our settings and now we are able to see if this fix works. After confirming the RPi is receiving data using the previous command, we can set it so it has these settings on boot.

```bash
sudo nano /etc/systemd/system/pms-uart.service
```

```text
[Unit]
Description=Set UART to 9600 baud raw mode for PMS sensor
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/bin/stty -F /dev/serial0 9600 raw -echo -echoe -echok -icrnl -imaxbel -isig -iuclc -ixany -ixon -ixoff min 1 time 5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target

sudo systemctl daemon-reexec
sudo systemctl enable pms-uart.service
sudo systemctl start pms-uart.service
```

```bash
sudo systemctl daemon-reexec
sudo systemctl enable pms-uart.service
sudo systemctl start pms-uart.service
sudo reboot
```

Now when it starts up again, check and ensure settings are maintained.

```bash
stty -F /dev/serial0 -a
```

Also run earlier timeout command and see if any data is received at this point to ensure it is working.

Create python file. 

```bash
nano pms.py
```

Here's the code to interpret the monitor and log our results.

![pythonscript]({{ site.baseurl }}/images/aq8.png)
![pythonscript]({{ site.baseurl }}/images/aq9.png)
![pythonscript]({{ site.baseurl }}/images/aq10.png)
![pythonscript]({{ site.baseurl }}/images/aq11.png)
![pythonscript]({{ site.baseurl }}/images/aq12.png)

and when ran:

```bash
python3 pms.py
```

![pythonscript]({{ site.baseurl }}/images/aq13.png)

Moving onto creating the plot, we will install a couple libraries.

```bash
sudo apt install octave
sudo apt install gnuplot
```

Create matlab file.

```bash
nano plot_pms.m
```

Here's the code used to create the graphs.

![matlab]({{ site.baseurl }}/images/aq14.png)
![matlab]({{ site.baseurl }}/images/aq15.png)
![matlab]({{ site.baseurl }}/images/aq16.png)

Now run the script:

```bash
octave pms_plot.m
```

And the final product:

![plot]({{ site.baseurl }}/images/aq17.png)

## Skills Demonstrated

- Embedded Systems Integration
- Linux System Administration
- Serial Protocol Implementation
- Python Data Pipeline 
- Octave Numerical Visualization
- Hardware-Software Integration
- Problem-Solving & Debugging
- Technical Documentation