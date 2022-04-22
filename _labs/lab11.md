---
title: "Lab 11: Localization (Sim)"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 11
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

In this lab, we implemented grid localization using Bayes filter. We were tasked to simulate the robot following a preplanned preprogrammed trajectory and, at every time step, determine where the robot is using Bayes filter (localization).

# Lab Task

## Grid Localization

The robot state is 3D and is given by (x, y, theta), its pose.

* x: [-1.6764, +1.9812) meters or [-5.5, 6.5) feet
* y: [-1.3716, +1.3716) meters or [-4.5, +4.5) feet
* theta: [-180, +180) degrees

To discretize these states, we use cells instead.

* x cell: [0, 12); 0.3048 m per cell
* y cell: [0, 9); 0.3048 m per cell
* theta cell: [0, 18); 20 degrees per cell

## Bayes Filter Algorithm

The bulk of the lab task is writing the python script for Bayes filter which is shown below:

<p align="left"><img src="../../images/lab11/bayes-new.png" height="750" width="750"></p>

The main idea of Bayes filter is that we have the belief of the robot (which is the probability of the robot being in a certain state (pose)), and we want to update this based on the control input and sensor values.

To understand this algorithm, we can go line by line and define all the parameters.

__Inputs__

* ``bel(prev_pose)``: This is the previous belief of the robot, where bel is a 3D matrix of probabilities that the robot would be located at each state.
* ``u``: This is the control input that has parameters delta_rot1, delta_trans, and delta_rot2 (explained in the ``compute_control`` function). This represents the control inputs that result in the position of the robot to change from the x_prev to x_cur.
* ``z``: The actual current sensor readings. This is in the form of 18 readings that are taken as the robot rotates 360º and takes readings every 20º increments. This is supposed to simulate ToF sensors that are located all around the robot and can take readings at different angles.

__For Loop__

* ``x(t)``: This for loop of x(t) is all the possible current poses (or states) of the robot. This includes all of the (discrete) x, y, and theta values that the robot could have.

__Prediction Step__

* ``bel_bar(cur_pose)``: This is the prior belief of the robot to be updated (I like to think of it as the intermediate belief after the prediction step but before the update step).
* ``p(cur_pose|u,prev_pose)``: This is the probability that you end up in a current position given a control input and a previous position.
* ``bel(prev_pose)``: This is the previous belief of the robot.
* Summation: This is the summation over all possible x_prev, which is basically all possible positions of the robot in the map.

__Update Step__

* ``bel(cur_pose)``: This is the belief to be updated for this state (and to be used as ``bel(prev_pose)`` in the next state).
* ``n``: This is the symbol for normalization (basically saying we need to normalize the belief so they all add up to 1).
* ``p(z|cur_pose)``: This is the probability that the current sensor readings at the current pose (18 readings taken for 360º) are correct given that the robot is at a particular position.
* ``bel_bar(cur_pose)``: This is the result from the prediction step.

## Bayes Filter Algorithm (Code)

Now that we kind of understand the actual Bayes filter algorithm, we need to implement it in Python. The structure given to us (which is super helpful) isolated certain parts and broke down each step which made writing the code simpler to understand.

### Compute Control

Compute control is a helper function that outputs u based on a current and previous position. Using the equations we went over in lecture, we can easily compute the ``delta_rot1``, ``delta_trans``, and ``delta_rot2`` that represents the control.

<p align="left"><img src="../../images/lab11/rot.jpeg" height="300" width="300"></p>

<p align="left"><img src="../../images/lab11/compute-control.png" height="300" width="300"></p>

It is important to note that the ``numpy.arctan2()`` function returns the value in __radians__, so I had to change that to degrees (which took a few hours to debug <span>&#9785;</span>).

< compute control python script >

### Odometry Motion Model

<p align="left"><img src="../../images/lab11/action-model.png" height="130" width="130"></p>

This is the probability that you end up in a current position given a control input and a previous position. It takes three inputs: ``cur_pose, prev_pose, u``. The ``u`` that is inputted is the actual control input based on the odometry sensor readings. The ``cur_pose`` and ``prev_pose`` are used to compute a new ``u`` that is a control input for some set of ``cur_pose`` and ``prev_pose``, calling this ``u_prop`` for u_proposed.

In order to compute this probability, as with every other probability we compute, we'll be using the gaussian function. The gaussian function takes in a sigma which determins the spread of the probability distribution curve, a mu, which determines the mean of the curve, and x, the input that results in that probability.

<p align="left"><img src="../../images/lab11/gaus.jpeg" height="400" width="400"></p>

The sigma for each type of value is given to us in the BaseLocalization class as a member variable. In order to get the probability, we need to get the individual probabilities for the ``delta_rot1``, ``delta_trans``, and ``delta_rot2`` elements of the control variables and multiply them together.

< motion model python script >

### Prediction Step

<p align="left"><img src="../../images/lab11/prediction.png" height="365" width="365"></p>

The prediction step takes in 2 inputs: ``cur_odom`` and ``prev_odom``. These are the actual current and previous positions of the robot from the sensors (which is close enough) to calculate the input ``u`` (the actual control input). 

In this step, we need 2 sets of 3 nested for loops: 1 set for the summation inside the line, which represents all the possible previous poses, and 1 set for the main outer loop. Each of the three nested for loops represent iterating through all the x, y and theta values (discrete cells). After obtaining the x, y and theta cell values, we want to change these values to the world coordinate values to get the ``cur_pose`` and ``prev_pose`` of some set of x, y and theta values for current and previous. We can use these as inputs to the motion model function above to obtain the probability that you go to this particular current pose given this particular previous pose and the actual control input. We multiply this with the belief (``loc.bel`` that we had from the previous run) at the previous pose. Finally we add all these values up for all the previous possible poses, and store them at the respective index for the current pose in ``loc.bel_bar``.

Having 6 nested for loops make this step *very very* computationally expensive. To speed up the process, we only want to actually go through these computations if the result to increment ``loc.bel_bar`` is not 0 (or not very very small). Thus, we can iterate through the prev_pose values and calculate what ``loc.bel`` would be for these values. If it's 0 (or close to 0, i.e., < 0.001), there is not point in doing the computations.

To end off the code, it's great practice to normalize ``loc.bel_bar`` since it is a probability distribution and should add up to 1.

< prediction step python script >

### Sensor Model

<p align="left"><img src="../../images/lab11/sensor.png" height="85" width="85"></p>

The sensor model computes the probability that the actual sensor reading we have is correct, given the pose of the robot. As with any probability function, we use the gaussian function to help us with this. The sigma is given to us.

The sensor model function takes in a parameter obs, which is a 1D 18 element array of measurements that is taken at some x, y and theta. This is to be used as our mu in the gaussian function. The input to our gaussian function is the actual current measurements we just took after rotating 360º which can be found stored in the variable ``loc.obs_range_data``. 

Since the function outputs a 1D array of 18 elements containing the probability of each "view", I made a for loop to calculate the probability for each of the 18 values and appended them to an array to be returned.

< sensor model python script >

### Update Step

<p align="left"><img src="../../images/lab11/update.png" height="280" width="280"></p>

Now it's finally time to go to the update step. To capture the most outer for loop, we need to iterate through all the possible current poses of the robot, which is 3 nested for loops. For every possible pose, we find the intermediate belief (``loc.bel_bar`` that was updated in the prediction step) and multiply that by the probability that our sensor readings (views) at our current position are correct given that we're at that particular pose. We then update the belief at each pose.

Again, since this is a probability distribution, we need to normalize ``loc.bel`` since all the values need to add up to 1.

< update step python script >

## Final Trajectory <span>&#9786;</span>

To see how well our Bayes filter algorithm works, we ran our robot through a preplanned trajectory and plotted three kinds of values along the path: the odometry (sensor) values, the ground truth, and our belief. The odometry values are meant to include loads of noise, hence it is way off from the ground truth. If our Bayes filter works, the belief data points should be near to our ground truth data points.

<p align="left"><img src="../../images/lab11/traj.png" height="700" width="700"></p>

* Green = Ground truth
* Red = Odometry (sensor) values
* Blue = Belief

## Video Demos

### Odometry, Ground Truth, Belief

<p align="left"><iframe width="720" height="260" src="https://youtube.com/embed/x1aLu3PYMFY"></iframe></p>
<p></p>

### Odometry, Ground Truth, Belief, Bel Bar

In this video, the 2D ``loc.bel_bar`` data grid that is shown on the plotter. It shows the values of ``loc.bel_bar`` at each grid (the lighter the cell, the higher the belief). This is to check that our prediction step (motion model) works and we're not just relying on our update step (sensor model). I would say that the prediction step is pretty accurate because, after a few steps, the high probability cells are concentrated near the robot, except for a few exceptions in the run.

<p align="left"><iframe width="720" height="260" src="https://youtube.com/embed/i06vWHfzrzI"></iframe></p>
<p></p>