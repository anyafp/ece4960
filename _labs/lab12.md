---
title: "Lab 12: Localization (Real)"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 12
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

In this lab, we were given a fully-functional and optimized Bayes filter that works on the virtual robot, and we want to see if it works as well on the real robot

# Lab Task

## Localization (Sim)

Since we were given the model code for the Bayes filter, we want to run that and test it out on the simulator first before doing anything with the actual robot just to make sure.

<p align="left"><img src="../../images/lab12/model-sim.png" height="700" width="700"></p>

The belief (blue plots) are not too far off from the ground truth (green plot), so we're good to go to the next step.

## Observation Loop

The observation loop is similar to that of [Lab 9](https://anyafp.github.io/ece4960/labs/lab9/){:target="_blank"}'s (Mapping), so I reused that code. The only difference is, because I used continuous rotation and sampled as frequently as I wanted to, I needed to change the sampling such that I only stored the sensor data every 20º. At first I was going to rewrite my code to implement the rotation that would stop at every 20º and get the sensor reading, but thanks to [Ben](https://bwagner2-git.github.io/){:target="_blank"}'s advice, I just used the same code and set the robot to sample when the theta angle reached 20º increments.

One caveat to this (brought up by [Jojo](https://jojoliangs.github.io/ECE4960_FastRobots/){:target="_blank"}) is that, if the robot was rotating too quickly, the sensor reading measured might not be at the angle that we had recorded as the sampling time is about 15ms. I think that my rotation was slow enough to overcome this problem, but I wanted to look at my results to see whether it was worth to go back and change my code.

## Localization (Real)

When we do the real localization with the robot, we only use the update step which uses the sensor model. Because of this, we only need to get the sensor data at a certain positions in the map (namely the ones that we had used in [Lab 9](https://anyafp.github.io/ece4960/labs/lab9/){:target="_blank"}) which were, in feet:

* (-3,-2)
* (0,3)
* (5,-3)
* (5,3)

Instead of integrating my [Lab 9](https://anyafp.github.io/ece4960/labs/lab9/){:target="_blank"} code into this lab's jupyter notebook, I ran Lab 9's code for each coordinate, extracted the data, and manually hardcoded it as the values to be used in this lab. What I should have instead done was spent the time to integrate the code into this lab's notebook so that everything could be sent via bluetooth with one run. However, the way I went about it is better for debugging in case I did something wrong with the integration.

### Good Beliefs

I had 2 good belief points which were at (-3,-2) and (5,-3).

<p align="left"><img src="../../images/lab12/bel-good.png" height="1500" width="1500"></p>

I manually plotted the ground truth of (-3,-2) and (5,-3) but in meters, and this is not seen in the figures because they are plotted right under the belief data points.

### Not So Good Beliefs

I had 2 not so good belief points which were at (0,3) and (5,3). In order to analyze how far the belief points actually were from the ground truth, I turned on the black and white grids to look at the grid squares.

#### (0,3)

<p align="left"><img src="../../images/lab12/bel-bad-03.png" height="1500" width="1500"></p>

For this coordinate, the belief is one (diagonal) grid away.

#### (5,3)

<p align="left"><img src="../../images/lab12/bel-bad-53.png" height="1500" width="1500"></p>

For this coordinate, the belief is one grid away. 

I don't think that the continuous rotation of my robot caused the inaccurate belief since the other two beliefs were pretty accurate.

One reason why these two coordinated may have been less successful with their localization may be because (0,3) and (5,3) were the coordinates that I localized first. This may have affected the rotation of the robot when it gathered the sensor readings as the wheels may not have caught as much dust (and hence had more friction) which made the rotation less smooth and hence drifted more, making the robot believe it was at the other grid location and not the actual one.

A better reason (that Kirstin mentioned when I asked her about it) that made more sense was that the not so good readings were at positions where the surroundings were more symmetrical. I only annotated the map grid lines that were nearer to the robot as that mattered more since the readings further away were noisier and not as sharp (not as concentrated).

The good beliefs were at positions were there was not as much symmetry with the near map grid lines, and had more distinct features that would be lost if the robot were to be positioned slightly off from the true position. For example, for coordinates (-3,-2) on the left, if the robot were positioned more to the right in the map, it would not detect the top wall at 90º. If it were positioned lower in the map, it would detect the box at 0º when it's not supposed to. As another example, for coordinates (5,-3) on the right, if the robot were positioned more to the left of the map, it would detect the box at the top at 90º. If it were positioned higher up in the map, it would not detect the box on the left at 180º.

<p align="left"><img src="../../images/lab12/good-ano.png" height="1500" width="1500"></p>

The not so good beliefs, on the other hand, they had symmetrical (near) surroundings. This encourages worse belief because there aren't as many distinct landmarks that would increase the belief of being at a certain spot. For the (5,3) position on the right, both the belief and ground truth are approximately the same distance from the line of symmetry. As for the (0,3) position, although the ground truth is on the line of symmetry and the belief is further away from this, the annotated lines are all still detected at both of these positions at angles that are not too far off.

<p align="left"><img src="../../images/lab12/bad-ano.png" height="1500" width="1500"></p>

# Acknowledgments

Big thanks (as always) to the course staff for explaining how Lab 12 works, Vivek for giving us the model code for the Bayes filter, and Kirsten for explaining the symmetrical mapping reasoning for why some of my beliefs were not so good.