---
title:      "How to build your own environment in Robosuite"
description:   "tired of read the source code"
date:       2024-12-23 12:00:00
author:     xiaoNiao
math: true
tags: [embodied AI, robotics]
---

# How to build your own environment in Robosuite


## Overview
<div style="text-align: justify; text-justify: inter-word;">

Robosuite is a highly renowned simulation environment for embodied AI. Numerous projects have been developed based on it, such as Robomimic and MimicGen.

Recently, I have been delving deeper into Robosuite because I find it well-suited for my project. Prior to exploring Robosuite, I had also experimented with other simulators and environments, like Sapien and OmniGibson.

Each time I start to familiarize myself with a new simulator, the process can be quite challenging and frustrating. Therefore, I am writing this blog to document some useful findings and tips that I discover along the way

</div>

## how to build your own environment.
<div style="text-align: justify; text-justify: inter-word;">

First, check the official document. 

Apparently, you can find it in this page 
![official tutorial](https://robosuite.ai/docs/tutorials/add_environment.html).
 But when I do as what this page told, I just can not figure out what I am doing. 

Therefore, I strongly recommend to take a look at those files in *./robosuite/enviroments/manipulation/*. after that you can find there would be 5 functions there for every task respectively, **_load_models, reward, _check_success, reset_internal, _setup_observables**.

Just like what has been told in issue#526: 
![screenshot](/assets/image/robosuite/screenshot.png)

among these 5 functions, the 2 most important ones are _load_models() and _setup_observables. Taking *./robosuite/enviroments/manipulation/lift.py* as an example: 
</div>


    def _load_model(self):
        """
        Loads an xml model, puts it in self.model
        """
        super()._load_model()

        # Adjust base pose accordingly
        xpos = self.robots[0].robot_model.base_xpos_offset["table"](self.table_full_size[0])
        self.robots[0].robot_model.set_base_xpos(xpos)

        # load model for table top workspace
        mujoco_arena = TableArena(
            table_full_size=self.table_full_size,
            table_friction=self.table_friction,
            table_offset=self.table_offset,
        )

        # Arena always gets set to zero origin
        mujoco_arena.set_origin([0, 0, 0])

        # initialize objects of interest
        tex_attrib = {
            "type": "cube",
        }
        mat_attrib = {
            "texrepeat": "1 1",
            "specular": "0.4",
            "shininess": "0.1",
        }
        redwood = CustomMaterial(
            texture="WoodRed",
            tex_name="redwood",
            mat_name="redwood_mat",
            tex_attrib=tex_attrib,
            mat_attrib=mat_attrib,
        )
        self.cube = BoxObject(
            name="cube",
            size_min=[0.020, 0.020, 0.020],  # [0.015, 0.015, 0.015],
            size_max=[0.022, 0.022, 0.022],  # [0.018, 0.018, 0.018])
            rgba=[1, 0, 0, 1],
            material=redwood,
        )

        # Create placement initializer
        if self.placement_initializer is not None:
            self.placement_initializer.reset()
            self.placement_initializer.add_objects(self.cube)
        else:
            self.placement_initializer = UniformRandomSampler(
                name="ObjectSampler",
                mujoco_objects=self.cube,
                x_range=[-0.03, 0.03],
                y_range=[-0.03, 0.03],
                rotation=None,
                ensure_object_boundary_in_range=False,
                ensure_valid_placement=True,
                reference_pos=self.table_offset,
                z_offset=0.01,
            )

        # task includes arena, robot, and objects of interest
        self.model = ManipulationTask(
            mujoco_arena=mujoco_arena,
            mujoco_robots=[robot.robot_model for robot in self.robots],
            mujoco_objects=self.cube,
        )

<div style="text-align: justify; text-justify: inter-word;">

To design a different scene, we should change the robot, arena, objects here. Besides, you have to register the task in *./robosuite/__init__.py* by adding a line: 

    from robosuite.environment.manipulation.yourtask import yourtask

after that, you can modify the parameters in *./robosuite/demos* or add the name of your task after the --environment.

</div>

## Summary

<div style="text-align: justify; text-justify: inter-word;">

Robosuite is a great simulator, but its official tutorial isn’t very beginner-friendly. I hope my blog can help you if you run into difficulties while creating your own environment. At the very least, it should save you some time. I wish I had more time to delve into the details in the future.

</div>