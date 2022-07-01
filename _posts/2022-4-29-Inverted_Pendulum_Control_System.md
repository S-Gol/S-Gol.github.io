---
title: "Inverted Pendulum Cartpole Control System"
date: 2022-4-29
tags: [Controls, Arduino]
excerpt: "Cartpole control on an EPS32"
mathjax: "true"
---

## Summary
As part of a system dynamics and controls course, I built and programmed an inverted pendulum (cartpole) control system. It uses an ESP-32 microcontroller, an MPU6050 IMU for angular data, and a stepper motor as input. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Pendulum/PendVideo.gif" alt="Pendulum Operation">

Per the project requirements, an IMU was to be used instead of a more traditional encoder-based measurement system. This was a challenge, as it required additional hardware - a slip ring to carry a signal from the pendulum to the base - as well as additional controls development in the form of filtering. In addition to the IMUs built-in low-pass filter, I implemented a complementary filter to fuse the accelerometer and gyroscopic data. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Pendulum/Picture1.jpg" alt="MPU Protoboard">

In addition to the above filters, the control system consists of a dual PID loop to control both the position and angle of the system. The cartpole successfully avoids the edges of the track and stabilizes itself using this system. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Pendulum/Picture4.jpg" alt="Control loop">

In order to tune the system in real-time, the WiFi capabilities of the ESP-32 were used to transmit UDP packets containing measured data to a nearby computer. Software was developed in both Arduino C++ (ESP32) and in Python (PC) to perform IO and data visualization. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Pendulum/Picture3.jpg" alt="PC control window">
