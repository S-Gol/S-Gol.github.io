---
title: "VHF Satellite Signal Reception"
date: 2020-8-1
tags: [SDR, radio, electrical]
excerpt: "Using VHF radio to listen to satellites in my backyard"
mathjax: "true"
---

## Summary

Just like everyone else in the beginning of the COVID lockdown, I was looking for additional hobbies to keep myself busy. This led me to discover the RTL-SDR - a low-cost, USB-based, software defined radio. Software defined radio (SDR) replaces the traditionally hardware-based components of a radio, allowing the user to record a wide range of frequencies and signal types. 

Many satellites broadcast backup signals and telemetry in the VHF band. Fortunately for me and other amateur listeners, this signal has a high strength and is omnidirectional - this means that it can be received with nothing more than a circularly polarized antenna, an SDR, and some specialized software. 

Using nothing but the RTL-SDR and salvaged material from home (lockdown, remember?) I built a quadrifilar helix antenna (QFH). 

<img src="{{ site.url }}{{ site.baseurl }}/images/VHF/QFH.jpg" alt="Homemade QFH antenna">

Despite being made from just a cardboard tube, 3d-printed parts, duct tape, and some old electric fence wire, the QFH received signals from orbiting satellites without any issue. The first signal I received was from a NOAA weather satellite. While most of their transmissions are in the L-band, they still broadcast low-resolution images in VHF. This mode is known as automated picture transmission, or APT. These are low-res grayscale images in the visible and IR spectrum. These can be made into a false-color image by blending the visible and IR bands. 

<img src="{{ site.url }}{{ site.baseurl }}/images/VHF/NOAA.jpg" alt="NOAA satellite image">

In addition to NOAA's satellites, the Meteor satellites operated by Roscosmos transmit in the VHF band. They have similar capabilities to the NOAA satellites, and transmit their backup data in the low-rate picture transmission mode, or LRPT. This mode has similar data to APT, but is of a slightly higher resolution. 

<img src="{{ site.url }}{{ site.baseurl }}/images/VHF/Meteor.png" alt="Meteor satellite image">