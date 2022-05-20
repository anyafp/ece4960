---
title: "Lab 13: Path Planning and Execution"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 13
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

Now that we've successfully completed Labs 1-12, we are finally at the point where we want to do path planning and execution using anything that we've learned in the past 12 labs. We have to get our robot to hit the waypoints listed below as fast and accurately as possible.

1. (-4, -3) [start]
2. (-2, -1)
3. (1, -1)
4. (2, -3)
5. (5, -3)
6. (5, -2)
7. (5, 3)
8. (0, 3)
9. (0, 0) [end]

<p align="left"><img src="../../images/lab13/path.png" height="400" width="400"></p>

# Implementation Details

Below is a simple overview of how the robot is going to hit the waypoints:

1. The robot has a goal state it wants to achieve (it starts off with the first waypoint).
2. It performs localization to find out its current belief state.
3. The input control is computed and it executes the first angle rotation, the distance translation, then the second angle rotation to theoretically achieve the goal state.
4. It localizes again. If it achieved its goal state (or within 0.25m of it), the next waypoint becomes the robot's new target state. If not, the robot will continue to try to reach the current goal state before updating it to the next waypoint.

## Target States

Since the waypoints given to us were in feet, we wanted to convert this to meters since this is the units that the belief is in (and since we rely heavily on the belief, we standardize it to this). We also want to know the angle, and this angle is the angle where the robot faces its next waypoint.

| Waypoint (ft) | Waypoint (m) | Angle (º) | 
| -------- | ----- | ------- |
| (-4, -3) | (-1.2192, -0.9144) | 45º | 
| (-2, -1) | (-0.6096, -0.3048) |  0º | 
| (1, -1) | (0.3048, -0.3048) | 300º |
| (2, -3) | (0.3048, -0.9144) | 0º | 
| (5, -3) | (1.524, -0.9144) | 90º | 
| (5, -2) | (1.524, -0.6096) | 90º | 
| (5,  3) | (1.524, 0.9144) | 180º | 
| (0,  3) | (0, 0.9144) | 270º |
| (0,  0) | (0, 0) | anything |

## Python and Arduino

There were several things that we needed to split up on the Python and Arduino side of things. We needed to do the update step and calculations in Python, and most of the other things in Arduino. Below is a diagram of how tasks were split up:

<p align="left"><img src="../../images/lab13/python-arduino.png" height="350" width="350"></p>

Now that we knew how to split up the tasks between the Python and Arduino side, we could go on to actually implementing each one.

# Python

As illustrated above, the python script is in charge of finding the belief and calculating the control input. The setup is as per usual in the previous labs, including the Localization and Mapper classes (thanks Vivek!) that help with getting the belief and other things like normalizing angles. We have the ```wp``` variable, which holds an array of all the waypoints and their angle. The ```perform_observation_loop``` function in the ```RealRobot``` class is called by the ```loc.get_observation_data()``` function call and uses the array of sensor values received from the robot and formats it as needed to get the belief, which is computed in the ```loc.update_step()```.

After connecting to bluetooth and instantiating all our classes, we need to initialize uniform belief to get an initial belief. Now we can finally start the robot. When we begin, we only want to get the belief and calculate the control inputs when we receive something from the robot. The rest of the code is pretty self-explanatory from the comments.

<script src="https://gist.github.com/anyafp/ad23929533057b4bbd6508a977804eb1.js"></script>

Since we have an if statement that determines if we have reached the end waypoint depending on the belief, if we do not accurately control the robot and drift to a point near the last waypoint, the robot will think that it had reached the last point and will end the program.

# Arduino

As mentioned above, the Arduino side took care of the localization spin, gathering and sending the sensor data. After waiting for control inputs from the Python side, it will execute those inputs and repeat the process. This was done in a big while loop instead of a for loop because we don't know how many tries it will take the robot to get through the 9 waypoints since it corrects itself if it misses a waypoint.

We reused most of the code from our previous labs, but the biggest drawback of working individually up to this point and deciding to work together only on the last lab was that it was a big pain to combine our code together. There were some parts where my code performed better (e.g. the spin for localization) and some other parts where another group member's code worked better (e.g. Ben's sending of data using bluetooth) and merging them was painfull because we had to make sure that the UUIDs matched, the pins for the motors and sensors matched, and other small things that turned out to be difficult to debug. But once we had that settled, we could begin implementing the code. 

# Runs

## Take 1

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/uieqZwjLLs8"></iframe></p>
<p></p>

## Take 2

In this run, the spin for the localization did not seem to go very well as only one wheel would spin while the other didn't move (even though this was the same code that wsa very reliable in Lab 9 and Lab 12). We were pretty lucky that the belief we got was still somewhat accurate. Just like the first run, the wheels got weaker and weaker as we progressed through the map.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/n2AMH12JBAw"></iframe></p>
<p></p>

## Reverse

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/2VqoVV9AWJY"></iframe></p>
<p></p>

# Acknowledgments

I worked on this lab with [Ben](https://bwagner2-git.github.io/){:target="_blank"} and [Chidera](https://pages.github.coecis.cornell.edu/ccw94/Fast-Robots/index.html){:target="_blank"}. Because we were able to use what we had done in the previous labs and put it all together to finish Lab 13, we were able to complete it in 11 hours straight in one day from 2pm - 1am. Big thank you to Kirstin and the TAs for letting us stay for so long and for Jade for doing his RF project till that late.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/aiY8pAgs5rU"></iframe></p>
<p></p>

Thank you to the groups that were there on Thursday night for making the lab super fun and being supportive of everyone's robot! It was nice to hear everyone cheering on other groups' robots. And for Krithik, Apu and Aarya's group for picking up dinner for us.

Another big thank you to the course staff for being so supportive throughout the semester!