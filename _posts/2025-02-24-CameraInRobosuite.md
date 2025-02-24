---
title:      "Another day with robosuite"
description:   "I need a comprehensive tutorial"
date:       2025-02-23 12:00:00
author:     xiaoNiao
math: true
tags: [embodied AI, robotics]
---

# 0

<div style="text-align: justify; text-justify: inter-word;">

I spent the whole day modifying the Robosuite code to suit my needs. Before diving in, I only read the tutorial and module sections of the official documentation. I have to say that the tutorial is too basic to guide most students through using the simulator—unless you're a genius or already have extensive experience.

Today, I aimed to achieve two things: First, I wanted to retrieve camera parameters and obtain depth and RGB images for 3D reconstruction. Second, I planned to write a demo to enable teleoperation and retrieve observation information based on different button presses.

</div>

# 1 

<div style="text-align: justify; text-justify: inter-word;">

It took me quite a while to discover that robosuite.utils.camera_utils already contains some well-written and useful functions. This module provides functions to retrieve the camera's intrinsic and extrinsic parameters. Additionally, there's a function called get_real_depth_map() that normalizes the depth based on the extent, with the output unit being meters after normalization. By the way, the CameraMover class in camera_utils is also helpful if you need to move the camera.

</div>

# 2

<div style="text-align: justify; text-justify: inter-word;">

The other task was to enhance demo_device_control.py so that I could obtain depth, RGB images, and the camera matrix regardless of where I moved or rotated the camera. If you have similar goals, you can modify the code in robosuite.devices.keyboard.

</div>


# 3 

This blog is just for my personal notes. 