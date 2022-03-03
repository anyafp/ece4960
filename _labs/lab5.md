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
<p align="left"><img src="../../images/lab5/motor-pins2.jpeg" height="600" width="600"></p>


__We recommend powering the Artemis and the motor drivers/motors from separate batteries. Why is that?__

This is because the motors draw a lot of current and the Artemis is not able to provide the power demand that the motors require, thus you need a seperate battery that can afford to release a lot more current than the MCU.

__Routing paths__

One way that we've attempted to suppress noise is to twist the wires and cables. This is because the positive and negative magnetic fields cancel each other out. I've decided to use longer wires as compared to shorter ones just in case the sensors/board does not reach the areas I would like to place them at in the car. I'd imagine having longer ones would be easier to manage (just either cut them and use heat shrink to connect them together again or stuff them under sensors) rather than have them too short and I would need to desolder them and use a longer wire (seems like more hassle). I also assumed that the noise is not too significant to be taking it into too much consideration (that being said, I avoided using unnecessarily long wires too).

# Lab Tasks

I first soldered the motor drivers to the Artemis board and connected make the connections as shown below:

<p align="left"><img src="../../images/lab5/motor-oscill.jpeg" height="600" width="600"></p>

I chose a 3.7V power supply so as to mimic the voltage of the batteries that we use (3.3V from the Artemis, 3.7V from the battery) and 5A current. I used the function `analogWrite()` to generate PWM signals to the 7 and 12 pins. I took the image below from the datasheet and referred to it when deciding what value to `analogWrite()` to each pin.

<p align="left"><img src="../../images/lab5/pwm-control.png" height="600" width="600"></p>

I first set pin 7 to 0 and pin 12 to 100 and got the oscilloscope output as shown below.

<script src="https://gist.github.com/anyafp/2233df9d85f6677e564823ad0609fece.js"></script>
<p align="left"><img src="../../images/lab5/oscill-100.png" height="600" width="600"></p>

Changing pin 12 to 200 gives this output:

<p align="left"><img src="../../images/lab5/oscill-200.png" height="600" width="600"></p>

These were expected results so I did not try other combinations of PWM values. I then moved on to taking my car apart and replacing the oscilloscope connections with connections to the motor. I used the code below instead to show that the motors can run in both directions.

<script src="https://gist.github.com/anyafp/99c13088001cc9c8639288d948f2f8ac.js"></script>

As you can tell from the code, the motor should run in one direction for 1s, pause for 1s, run in the other direction, and pause for another second before repeating this cycle. I was supposed to use the power supply but jumped straight to using the battery because the battery supplied a more stable current than the power supply (i.e. the motor was more jittery with the power supply). Below is a video showing the motors running with the battery. The pause in the video seems less than 1 second because it takes some time for the motors to come to a stop (which eats into the pause time).

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/EqGnz6_-7m8"></iframe></p>
<p></p>

Next, I soldered the second motor similar to the first (with the connections shown in the Prelab) and ran this code on the Artemis:

<script src="https://gist.github.com/anyafp/6922b98aac0f3f95de7d4318f733c419.js"></script>

Similar to the example with one motor, the exact same thing should happen but this time, to both motors simultaneously as shown below:

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/1xp8zGrOSL4"></iframe></p>
<p></p>

Now that I knew that both motors work, I could secure everything in the car and run it on its own.

<p align="left"><img src="../../images/lab5/car-connect.jpeg" height="600" width="600"></p>