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
* ``p(cur_pose | u, prev_pose)``: This is the probability that you end up in a current position given a control input and a previous position.
* ``bel(prev_pose)``: This is the previous belief of the robot.
* Summation: This is the summation over all possible x_prev, which is basically all possible positions of the robot in the map.

__Update Step__

* ``bel(cur_pose)``: This is the belief to be updated for this state (and to be used as ``bel(prev_pose)`` in the next state).
* ``n``: This is the symbol for normalization (basically saying we need to normalize the belief so they all add up to 1).
* ``p(z | cur_pose)``: This is the probability that the current sensor readings at the current pose (18 readings taken for 360º) are correct given that the robot is at a particular position.
* ``bel_bar(cur_pose)``: This is the result from the prediction step.

## Bayes Filter Algorithm (Code)

Now that we kind of understand the actual Bayes filter algorithm, we need to implement it in Python. The structure given to us (which is super helpful) isolated certain parts and broke down each step which made writing the code simpler to understand.

### Compute Control

Compute control is a helper function that outputs u based on a current and previous position. Using the equations we went over in lecture, we can easily compute the ``delta_rot1``, ``delta_trans``, and ``delta_rot2`` that represents the control.

<p align="left"><img src="../../images/lab11/rot.jpeg" height="300" width="300"></p>

<p align="left"><img src="../../images/lab11/compute-control.png" height="300" width="300"></p>

< python script >

### Odometry Motion Model (Transition Probability/Action Model)

<p align="left"><img src="../../images/lab11/action-model.png" height="130" width="130"></p>

This is the probability that you end up in a current position given a control input and a previous position. It takes three inputs: ``cur_pose, prev_pose, u``. The ``u`` that is inputted is the actual control input based on the odometry sensor readings. The ``cur_pose`` and ``prev_pose`` are used to compute a new ``u`` that is a control input for some set of ``cur_pose`` and ``prev_pose``, calling this ``u_prop`` for u_proposed.

In order to compute this probability, as with every other probability we compute, we'll be using the gaussian function. The gaussian function takes in a sigma which determins the spread of the probability distribution curve, a mu, which determines the mean of the curve, and x, the input that results in that probability.

<p align="left"><img src="../../images/lab11/gaus.jpeg" height="400" width="400"></p>

The sigma for each type of value is given to us in the BaseLocalization class as a member variable. In order to get the probability, we need to get the individual probabilities for the ``delta_rot1``, ``delta_trans``, and ``delta_rot2`` elements of the control variables and multiply them together.

< python script >

### Prediction Step

<p align="left"><img src="../../images/lab11/prediction.png" height="365" width="365"></p>

< python script >

### Sensor Model

< python script >

### Update Step

< python script >

## Video Demos

### Odometry, Ground Truth, Belief

<p align="left"><iframe width="720" height="265" src="https://youtube.com/embed/-gU4H-rbjQE"></iframe></p>
<p></p>

### Odometry, Ground Truth, Belief, Bel Bar

<p align="left"><iframe width="720" height="265" src="https://youtube.com/embed/-U1yAXGzZj8"></iframe></p>
<p></p>