---
title: "Lab 6: PID Speed Control"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 6
layout: lab
---
In this lab, we used what we learned from lecture about PID control and implemented it to our car. We first had to set up the bluetooth connection and make sure that the Artemis and laptop could communicate information smoothly so that the laptop can use the information from the sensors to make necessary adjustments, and send that information back to the Artemis and so on.

# Prelab - Sending Data Over Bluetooth

In order to get the lab to run smoothly, we need to send data to our laptops from the sensors on the car. Since I was planning on doing Task A, I focused on sending the data of only the front ToF sensor. It would be way too slow to send the data everytime I receive new data (ie every 0.5s) so the best way to go about this is the store the data first in the Artemis, and send it over when needed. The steps I took are as follows:

1. Laptop --> Car: Manually send `start_task()` command.
2. Car: PID control while storing ToF data, speed, and timestamps in arrays.
3. Laptop --> Car: Manually send `stop_task()` command when I see that the task has been completed.
4. Car --> Laptop: Sends the number of data points collected (which is either the size of the array or less) when `get_size()` is sent from laptop to car.
4. Laptop: Goes through a for loop and requests from the car the ToF data, speed and timestamps at every index from car. Car sends data at every index. Laptop stores it in an array. This step can take quite a while if we have a lot of data points.
5. Plot graphs.

When I'm storing data, I don't want to store too many data points unnecessarily so I chose to store readings every 0.5s. Since the rate I store the data at is slower than the sampling rate, I had two separate functions for them, `store_data()` and `sensor_data()`. Another issue that I wanted to combat is ensuring that I don't exceed the internal RAM of 384kB. After testing out how many data points I need to test Task A, I found out that an array size of 150 is sufficient. I would increment the index, and reset it to 0 if I exceed the array size. This is where the timestamps come in handy because it wouldn't matter where the data points are in the array if they correspond to their timestamp.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/9ae0be747ff4c55c6a6a3d5ed31cc077.js"></script>

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/fb28dc42352e5dfc9ad7bfc2eac0f925.js"></script>

# Lab Tasks

## Task A - P(ID)

For this task, we had to use PID control to ensure that the car stops 300m (30cm) away from the wall. In order to do this, I first used only the P in PID. I calculated the error based on the ToF sensor reading and the target distance, obtained the speed from this value and a Kp value, and set the motor speed based on this as shown below:

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/c574ca392cbf173d52032e216ce01424.js"></script>

I added a limiting range function to ensure that even as the speed goes beyond the maximum or below the minimum, it would still move.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/b6b1b1bc855544e59bfc137e753602c2.js"></script>

After ensuring that the P(ID) control works, I needed to figure out what Kp value works. I did a lot of trial and error to find the values and these are the plots generated with different starting points and Kp values.

Below is the video demonstration and data points with the Kp = 0.03, starting point = ~2.5m configurations. The video shows the three runs I did to ensure consistency, and the graph shows the three data plots for each run (although the videos look almost identical, it was either that I accidentally used the same video thrice, or that I just took very very consistent videos... you can see in the plots that they are actually different runs).

<p align="left"><img src="../../images/lab6/A-P.png" height="1000" width="1000"></p>

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/1yALPxBgoug"></iframe></p>
<p></p>

As you can see from the video, with a Kp value of 0.03, the car almost hits the wall before moving back slightly. This can also be seen in the data plots where the distance hits close to 0 before oscillating once to reach 300mm. The reason the car overshoots is because it's only using the proportional value of the error and does not take into account the integral or derivative and hence I tried including the derivative.

Although the graph shows that the final distance is around 300mm, I printed the last data point that the sensor read to see what the actual reading was.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/fab13695221d689c71922cf75eb44726.js"></script>

It seems like the value was not actually 300mm although according to the conditions set, it should theoretically only stop when it reaches exactly 300mm, but this disparity between what is expected and the actual values is not shocking.

## Task A - P(I)D

I added in the equation to find the derivative and add it to the speed of the motors.

<style type="text/css">
  .gist {width:750px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 750px;}
</style>

<script src="https://gist.github.com/anyafp/b975c26ea27371440354baedc86c5074.js"></script>

I tried different variations of Kp and Kd and found that Kp = 0.03 and Kd = 0.05 worked better than the previous run. Below are the video demo and data plots.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/atbrFemJw8E"></iframe></p>
<p></p>

<p align="left"><img src="../../images/lab6/A-PD-2m-03-05.png" height="1500" width="1500"></p>

As seen in the plots above, the car takes less oscillations to reach the stable state (300mm) and less time to do so, which indicates that it performs better than only having the P(ID) control. This, however, is very dependent on the Kp and Kd values chosen as I had to do a lot of trial and error with the values before getting a run that performed better than only the P(ID) control.