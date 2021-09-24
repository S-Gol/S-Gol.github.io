---
title: "Senior Design Project - Helicopter Pusher Prop"
date: 2021-5-1
tags: [Senior Design, Machining, Instrumentation, Mechanical, Mech, Design]
excerpt: "My senior design project - development and testing of an experimental pusher prop for Sikorsky."
mathjax: "true"
---

## Summary

My senior design team was tasked with designing, building, and testing a pusher prop test rig by Sikorsky / Lockheed Martin. The test rig is intended for use by Sikorsky in the testing of innovative wing-tip devices - these devices and their function are protected under an NDA. The test rig needed to be capable of rotational speeds of 3600 RPM, a sustained tip speed of 0.67 mach, and provide actuation of various rotor-mounted devices by use of a cam system. Instrumentation of the rig included cameras, strain gauges, thermistors, motor instrumentation, and hall effect sensors. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Sikorsky/LowSpeed.gif" alt="Low-speed rotation of the test rig. ">

My role in this project was primarily focused on system integration, mechanical design, and manufacturing. I performed mechanical design and analysis on these systems:

1. Cam system and mounting hardware
2. Blades
3. In-blade actuation system
4. Airfoils
5. Mounting system for Sikorsky's proprietary devices

Shown below is an image of the SolidWorks assembly and various component locations. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Sikorsky/SystemLabels.png" alt="SW assembly and location diagram.">

All parts were designed using SolidWorks. FEA was performed on all parts under dynamic centrifugal loading conditions using the SolidWorks simulation package. Considering the high speed rotation of the rig, it was absolutely necessary to ensure the safety of all parts. In addition to ensuring acceptable stresses in all parts, vibrational loading needed to be considered. Because of this, the team designed all parts to have dual retaining features - pins were safety wired, bolts had locking fasteners, thread-lock adhesive was used on screws, and bearings were designed to fail in a safe manner. Safety features of the entire rig were designed to Sikorsky's aircraft design standards. 

In addition to getting parts custom-made by 3rd-party shops, the team made many of the parts by hand. I was able to work on the blades, airfoils, and cam system extensively in the University machine shop, gaining experience with everything from milling machines to 3d printing to composite hand-layups. 

After almost a year of working on the project, the team was able to succesfully test the rig. It performed flawlessly up to the maximum speed allowed by the University. 

Below is a video of a test at 550RPM - Note the signficant aliasing due to the camera's framerate. 

<img src="{{ site.url }}{{ site.baseurl }}/images/Sikorsky/550RPM.mp4" alt="Video of a medium-speed test.">

