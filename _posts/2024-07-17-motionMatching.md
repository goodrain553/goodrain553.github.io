---
title:      "Motion Matching - part I"
description:   "A new way to next generation of animation"
date:       2024-07-17 12:00:00
author:     xiaoNiao
catalog: true
mathjax: true
tags: [computer graphics,game develop]
---

# Motion Matching - part I

Motion Matching is an effective technique for generating human animations. After being presented at GDC 2016, it has gradually gained more widespread recognition. The game *FOR HONOR*, which I'm quite fond of, was one of the first to utilize this approach.

The game's visuals exhibited an exceptionally high level of expressiveness, with character animations that were remarkably fluid. However, the game itself may not have been engaging enough for a larger player base.

Nowadays, more and more games are adopting motion matching in their development, such as *FAR CRY 6* and *BLACK MYTH: WUKONG*. Additionally, there are now third-party software solutions that support motion matching integration on platforms like Unreal Engine 5, Unity, and GitHub.

**So, why do we use motion matching?**

## Background and Motivation

For a long time, many games have relied on finite state machines to represent character motions. This approach can be traced back to the *"Motion Graph"* technique presented at the 2002 SIGGRAPH conference.

The state machine approach works well when the number of motion states is relatively small. However, as the motion state machine becomes more complex, its robustness and stability can start to decline. Even a minor modification can lead to unpredictable consequences.

Another drawback of the motion state machine is its limited responsiveness. It can be challenging to determine the appropriate action to take when a player provides a new input during a specific motion.

From an industry perspective, animators often have to spend significant time and effort segmenting actions, which can be a repetitive and labor-intensive task.

It is precisely due to these three key limitations that the emergence of Motion Matching has become highly valuable.

However, before the development of Motion Matching, there was another academic paper called *"motionFields"* that had already proposed the core idea of Motion Matching for interactive character locomotion. Unfortunately, the complicated mathematical calculations required for finding and blending the closest motions meant that this approach could not be effectively implemented in computer games at the time.

Fortunately, a few years after the *"motionFields"* research, Ubisoft developed and implemented Motion Matching in a commercial video game.

The next section will delve into the core principles of this new technology.

## Method 

In the *Motion Matching* approach, games continuously search the motion dataset to find the motion with the lowest cost at every frame. While it is commonly claimed that this detection process is performed once per frame, the actual implementation may involve a fixed number of detection instances per second, such as 30 times per second. Increasing the frame rate would not necessarily increase the number of detection instances.

The key aspects that need further discussion are:

+ **HOW TO CREATE THIS DATASET?**

+ **WHAT IS THE COST?**

When creating dataset, producers need to design some dance cards, which tell the actors/actresses what to perform, what route to walk along, what motion should be done at specific spots. And these dance cards should be carefully splited according to the task.

here are some examples of dance cards, which are from Ubisoft and Game Science.

![](https://github.com/goodrain553/goodrain553.github.io/raw/master/_posts/img/dance_card1.png)

After lots of attempt, some producers (e.g. Game Science) categorize these motions into two parts, *free movement* and *directional movement*. Free movement refers to those whose face faces to the same direction as their body. And directional movement refers to those with face facing one direction. For instance, the soilder aiming at the target moves from right to left. And then, these two major categories are further divided into five sub-categories. 

| Free Movement | Directional Movement |
| --- | ---|
| spot turn | spot turn |
| S-shaped route（advance）| S-shaped route（advance and backward）|
| Circling | Circling | 
| Shuttle run | Diamond and Square |
| Fartlek | 米字跑(move along the shape like character "米") |

this video shows more details about how to design dance cards.

[游戏科学_招文勇_motion_matching](https://www.bilibili.com/video/BV1GK4y1S7Zw)

In my experience, designing dance cards and capturing motion data are highly practical endeavors. This means we can gain valuable insights by learning from the experiences of others, but we can truly understand the underlying difficulties only when we confront and overcome them ourselves.

Moving on, let's discuss how the cost is calculated in the Motion Matching approach.

At every frame, the game engine searches the motion dataset to find the corresponding motion with the lowest cost. In many data mining algorithms, we design a metric to measure the distance or similarity between different samples, as long as that metric obeys three key rules:

+ $m(a,b) \geq 0$
+ $m(a,b) = m(b,a)$
+ $m(a,b) \leq m(a,c) + m(c,b)$

$L_2$ norm is a typical metric.

In motion matching, the process can be descrbed as follow

$$
nextPose = \mathop{min}\limits_{i \in Dataset} {\|x_{curr}-x_{i}\|}
$$

where $x$ is a feature vector.

the feature vector may contain these as elements:

+ root/linear velocity
+ position of end effectors w.r.t. root joint 
+ root/linear velocity of end effectors w.r.t. root joint
+ future heading position/orientation 
+ foot contacts

By doing this, we can get the lowest cost motion for next frame. 


In the next part, I am going to delve into the code of motion learning based on the speech on GDC, open-source project of Learned Motion Matching, a new innovation of motion matching based on learning theory, and technique file of UE5.


## reference

[GDC 2016 Motion Matching, The Future of Games Animation](https://www.youtube.com/watch?v=KSTn3ePDt50)

[GDC 2016 Motion Matching and The Road to Next-Gen Animation](https://www.gdcvault.com/play/1023280/Motion-Matching-and-The-Road)

[游戏科学_招文勇_motion_matching](https://www.bilibili.com/video/BV1GK4y1S7Zw)

[Motion_Field_for_Interacitve_Character_Locomotion](https://dl.acm.org/doi/pdf/10.1145/2602758)

[GAMES_105_计算机角色动画基础](https://www.bilibili.com/video/BV1GG4y1p7fF)

[Motion Matching - 'Dance Card' Breakdown](https://www.youtube.com/watch?v=_Bd2T7uP9VA)