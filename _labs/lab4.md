---
title: "Lab 4: Putting It All Together"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 3
layout: lab
---

## Objectives

* Code the Arduino to use the ultrasonic sensor.
* Get the robot to detect two objects using the ultrasonic sensor.
* Get the robot to navigate through obstacles.

## Introduction

In this final lab, we will combine everything (or most of it) we had done thus far, and additionally adding an ultrasonic sensor for ranging and obstacle sensing. By the end of the lab, we would have a robot that is able to navigate through an obstacle course.

## Adding the Ultrasonic Sensor

For the first part of the lab, we'll only be utilizing the microphone and ultrasonic sensor (US) to test out its functionality. We first added the US to the nose of the robot and shifted around some parts such that everything would be evenly balanced as seen below.

![US circuit](../../images/lab4/us-circuit.jpeg)

Because the equipment we use is not always error free, we needed to characterize the US to see how accurate it is and obtain the error. From there, the readings would need to be calibrated. In order to characterize the US, the measurements from the US were obtained and compared to the physically measured distances, over a set of distances. To code the US, we used the code that we had gone through in class.

![US Graph Error](../../images/lab4/us-graph.jpeg)

As seen in the graph above, the US was pretty accurate and the error was minimal. The error was measured by taking the absolute value of the difference between measured distances and the actual distances, and, as seen in the graph, the error did not exceed 2.5cm (and was mostly close to less than 1cm).

## Rebuilding the Circuit

After that, we built the circuit to incorporate both the microphone, motor, and US onto the breadboard. Below is an image of the final circuit.

![Circuit](../../images/lab4/breadboard-circuit.jpeg)

## Demo 1

The main purpose of this lab was to test the functionality of our microphone, motor, and US. In order to do so, we were to code our robot such that it followed certain instructions. This task required a little bit of set up in the environment such that the robot could react to it accordingly. Below is an image taken from the lab handout that illustrates the setup that was required.

![Set Up](../../images/lab4/setup.png)

The final and biggest part of the first half of this lab was to code the robot such that it followed the instructions below:

1. The robot initially sits still with the LED off.
2. The moment the robot detects a sound that is 550Hz (and no other frequency), it is to rotate 360º over a duration of no less than 5s. 
3. As it spins around, at some point it would end up facing an obstacle. At this point, the US should detect the object and the LED should light up. 
4. When it passes the object and no longer faces it, the LED should turn off.
5. When the robot faces the second obstacle, step 3 should repeat.
6. The robot should end off come to a stop at the same position it had started.

There are also some constraints to the code:
* No library other than the FFT library should be used.
* Functions like `attachInterrupt()`.
* Functions like `delay()` or any blocking function or blocking code should not be used.
* RX and TX LEDs must remain off during the video recording.

Coding the robot was not simple, especially with the constraints above. The first step was to perform FFT on the signal from the microphone. This was done by taking the code from the last part of Lab 3 and making it into a function. Since there were so many segments to the code, it was made more readable by putting anything that was more than 10 lines of code into a helper function.

Once the ADC and FFT conversion were done, the next step was to trigger the detection of a sound at 550Hz. In order to do so, the output spectrum of the sound demo played needed to be analyzed. Below is the spectrum obtained from the audio file.
![Sound Graph](../../images/lab4/sound-graph.jpeg)

Since the frequency of the other two sounds were 700Hz and 900Hz, I could set my range for detection to be 450Hz - 650Hz so that it is guaranteed that the 550Hz sound would be detected.

Once the 550Hz frequency sound is detected, the motors would start and the LED would be set as off (just in case). Then come the conditional statements that track whether there is an obstacle in front of the robot. I used the code we had used in class for the US and made it into a helper function that would return the distance to the object. This distance would constantly be updated, and the LED would turn on if the US detected an object that was less than 80cm away.

In order to get the robot to turn 360º in around 5s, I initially set the loop to run for 5000ms, before calibrating this value to match the actual duration it took when testing out the robot.

Here is the final video for demo 1:
<p align="center"><iframe width="720" height="408" src="https://youtube.com/embed/2BM98UhIqjY"></iframe></p>