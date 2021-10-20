---
title: "Geostationary Satellite Signal Reception"
date: 2021-8-1
tags: [SDR, radio, electrical]
excerpt: "Using L-Band radio to listen to satellites"
mathjax: "true"
---

## Summary

After using VHF for a while, I decided to move to something more complex - L-band satellite reception. This frequency provides a much higher data rate, at the expense of having a much lower power. This low-power signal requires the use of a parabolic dish to amplify the signal. Other hobbyists use small dishes to provide this amplification - modern, 2-foot DirecTV style dishes often provide enough gain. However, I want to also be able to use this setup for other projects - hydrogen line astronomy, measuring pulsars, and X-band satellites are all things I'm planning to work on in the future. These other projects all require a much larger dish. 

Since I wanted to make this project as future-proof as possible, the dish needed to be large. My initial goal was a diameter of 8 feet. The precision needed makes it impractical to make a dish of this size by hand. Fortunately, I happened to spot a run-down old TV dish in a backyard - and the owner offered to give it to me for free, in exchange for the labor to take it apart. 

Taking it apart, of course, was no easy task. The 12-foot dish was almost 30 years old, and had sat unused for 20. It had trees growing through it, squirrel nests inside it, and parts rusty enough to fall apart if you looked at them wrong. Fortunately, the vital components were largely aluminum and thus undamaged. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/Dish_Old.png" alt="Dish before disassembly">

Re-assembly of the dish required significant work. All parts got sandblasted and a fresh coat of paint. New actuation mounts had to be welded on. Rusty hardware was completely replaced. A new counterweight was made to balance the significant weight of the dish. 

After refinishing all the parts, the next big step was figuring out how to mount it safely - a 12' diameter dish has a lot of drag in bad weather. One 5-foot hole, a 12 foot steel tube, and about 200 pounds of concrete later, the post was upright - and not going anywhere. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/Moving.gif" alt="Dish moving with actuator">

After attaching my hand-made L-band circular waveguide antenna, I was ready to use the new dish. The first task was reception of NOAA's GOES satellites. These weather satellites are in geostationary orbit and broadcast images of the earth roughly ever half-hour in various wavelengths. Here's an example of one of the single-channel images - in this case, red visible light. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/Red.jpg" alt="GOES visible light channel">

While GOES does not broadcast full-color images, they can be false-colored based on the varying wavelengths. I wrote a Python script to perform this colorization, using a Color Lookup Table (CLUT) to map the visible and IR wavelengths to color. This provided high-resolution color images of the entire earth. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Dish/US.png" alt="GOES false-color CONUS">

Having successfully received GOES, my next goal is to automate the motion of the dish in order to measure galactic hydrogen radiation. This will require a new antenna, upgraded actuation hardware, and an automatic computer control system.  