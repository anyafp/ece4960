---
title: "Lab 5: Open Loop Control"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 5
layout: lab
---

In this lab, we changed from manual to open loop control of the car. At the end of this lab, our car executed a pre-programmed series of moves, using the Artemis board and two dual motor drivers.

# Prelab

__What pins will you use for control on the Artemis?__

<p align="left"><img src="../../images/lab5/motor-pins1.jpeg" height="600" width="600"></p>

__We recommend powering the Artemis and the motor drivers/motors from separate batteries. Why is that?__

This is because the motors draw a lot of current and the Artemis is not able to provide the power demand that the motors require, thus you need a seperate battery that can afford to release a lot more current than the MCU.

__Routing paths__

One way that we've attempted to suppress noise is to twist the wires and cables. This is because the positive and negative magnetic fields cancel each other out. I've decided to use longer wires as compared to shorter ones just in case the sensors/board does not reach the areas I would like to place them at in the car. I'd imagine having longer ones would be easier to manage (just either cut them and use heat shrink to connect them together again or stuff them under sensors) rather than have them too short and I would need to desolder them and use a longer wire (seems like more hassle). I also assumed that the noise is not too significant to be taking it into too much consideration (that being said, I avoided using unnecessarily long wires too).

# Lab Tasks

I first soldered the motor drivers to the Artemis board and connected make the connections as shown below:

<p align="left"><img src="../../images/lab5/motor-oscill.jpg" height="600" width="600"></p>

I chose a 3V power supply so as to mimic the voltage of the batteries that we use (3.3V from the Artemis, 3.7V from the battery). I used the function `analogWrite()` to generate PWM signals to the 8 and 12 pins. I took the image below from the datasheet and referred to it when deciding what value to `analogWrite()` to each pin.

<p align="left"><img src="../../images/lab5/pwm-control.png" height="600" width="600"></p>

I first set pin 8 to 0 and pin 12 to 100 and got the oscilloscope output as shown below.

<script src="https://gist.github.com/anyafp/2233df9d85f6677e564823ad0609fece.js"></script>
<p align="left"><img src="../../images/lab5/oscill-100.png" height="600" width="600"></p>

Changing pin 12 to 200 gives this output:

<p align="left"><img src="../../images/lab5/oscill-200.png" height="600" width="600"></p>

These were expected results so I did not try other combinations of PWM values. I then moved on to taking my car apart and replacing the oscilloscope connections with connections to the motor.