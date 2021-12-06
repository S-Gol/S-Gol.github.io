---
title: "Fully-Automated satellite reception"
date: 2021-12-1
tags: [SDR, radio, electrical]
excerpt: "Fully-Automated satellite reception"
mathjax: "true"
---

## Summary
Using the L-Band satellite dish I previously sourced for use with GOES, I decided to take it one step further. There are several LEO satellites that transmit with a mode called HRPT - this provides high-res, close-up images of the Earth. The challenging part of receiving these satellites is their motion - the low orbit necessitates fast and precise tracking, which I didn't think was practical to do by hand. 

In order to automate the tracking process, I developed an automated control system for the satellite dish using a Raspberry Pi, linear actuators, custom-made hardware, and various other parts.  

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/Mill.jpg" alt="Making AL parts">

After designing the assembly, I started by making the parts. The linear actuators required custom mounting hardware to interface with the 30+ year old dish - which was really not intended to be modified in this way. I built them using resources available in my school's machine shop, including milling by hand on a Bridgeport machine and using a CNC mill for the more intricate geometry. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/DishElecHardware.jpg" alt="Dish electrical system">

Next was developing the electrical hardware. The dish had a large, plastic cover where the previous LNB sat. Because my radio hardware took up significantly less space, I was able to use the remaining space to house the electrical system. This features a RaspPi, two H-Bridge style motor drivers, the SDR and antenna, an IMU for checking rotation, and a breadboard for the current prototyping phase. The entire system is built on 2020 Aluminum extrusion, allowing for easy assembly and adjustment as needed. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/polardiagram.jpg" alt="Dish diagram ">

One other challenge was figuring out the kinematics of the dish. Since it was designed originally to be stationary, it has a number of features designed to make it easy to perform a one-time adjustment. However, the large number of angles and offsets require some significant math to convert between actuator positions and a usable azimuth/elevation. To make this easier, I developed a Python script to calculate the rotation matrices for each step and integrate the position data using Euler's method to keep an up-to-date rotation. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/DishTest1.gif" alt="Dish moving under power of the RaspPi ">

After that, all that was left was to put it together and test it. Shown in this gif is an early test, using the arrow keys to manually "jog" the actuators. I haven't yet been able to receive an image from the HRPT satellites - the current can-tenna configuration does not have the correct polarization. After I build a helical antenna, it should be able to receive from these satellites with no issue.  


