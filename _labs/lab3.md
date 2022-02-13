---
title: "Lab 3: TOF & IMU"
date: 2018-11-28T15:14:54+10:00
featured: true
weight: 3
layout: lab
---

In this lab, we equipped the robot with sensors (specifically the Time-of-Flight (ToF) and Intertial Measurement Unit (IMU) sensors). 

# Prelab

## I2C Bus Address of ToF

From the datasheet of the Tof (Time-of-Flight) sensor, the I2C bus uses the device address 0x52. Since we're using two sensors, we need to distinguish the data received from each ToF sensor. We can do this in two ways:

__Change the address programmatically (while powered)__

This method changes the I2C address of each ToF sensors so that we can distinguish between the data received from each one. The benefit of this method is that it would allow us to communicate with both ToF sensors simulatneously without having to shut down either one of the sensors. This method would also reduce delays when extracting the data from the ToF sensors. A disadvantage of this is that we would need to change the I2C address every time we power off the sensor as the address resets to the original one (0x52).

__Enable sensors separately through their shutdown pins__

Using this method would require us to pull the shutdown pin down to shut down one of the sensors so that we can extract the data using the I2C address (which both sensors share). This method would save power since we would be powering off a sensor when reading data, and we would not need to change the address of the sensors every time they power off. Another advantage to this method would be, depending on how we use the sensors, if there is not much need for both sensors, we can leave one of them shut down for the periods of time when it is not needed. The disadvantages of this method is that we would need to write additional logic to program the pulling up and down of the shutdown pins, and ensure that they are not both enabled at the same time. Another disadvantage is that there might be delays in the communication with the sensors due to the enabling and disabling.

## Position of ToF Sensor

According to the datasheet, the range of the sensor is as follows:

| Distance Mode | Max Distance (Dark) (cm) | Max Distance (Bright) (cm) |
| --------- | -------- | --------- |
| Short | 136 | 135 |
| Medium | 290 | 76 |
| Long | 360 | 73 |

And the angular sensitivity of the sensor is related to the field of view.

<p align="left"><img src="../../images/lab3/fov.png" height="600" width="600"></p>

With a 16x16 ROI (region of interest), it is possible to get a 27º diagonal field of vision.

With this range and angular sensitivity in mind, I've decided to place both ToF sensors at the front of the robot, side-by-side. This would result in a slight overlap in the field of view which would allow room for error correction when comparing the data received by both sensors.

However, the robot would miss obstacles if they do not fall in the field of view which might be inevitable since the diagonal field of view is 27º.

# Lab Tasks

## ToF

### Scanning I2C Address

Using one ToF sensor, the address printed was 0x29. This is because the address is bit shifted and last bit is for the read write status.

<p align="left"><img src="../../images/lab3/i2c-add.png" height="600" width="600"></p>

### ToF Modes

The ToF sensor has 3 modes as shown in the table in the prelab section. The Long mode is the best to capture longer ranges, but it works best in a darker environment. If the environment is very bright, the Short mode would allow for more range.

### Read Distance

Next, we tested the ToF sensor using the SparkFun readDistance example code. In the first half of the video, I was measuring the distance at 15cm (the length of the ruler) from the sensor to the wall. As I adjusted the position of the ToF sensor, you can see that the distance printed on the serial monitor changes. The distance does not seem super accurate, and we'll probably need to calibrate the sensor to get the true distance value.

In the second half of the video, I tried using a different a different texture and color for the surface that the sensor was facing. The distance seems slightly greater than actual, but it does not seem like there is a significant difference.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/tutPGJ5YOR4"></iframe></p>
<p></p>

### 2 ToF Sensors in Parallel

Next, both ToF sensors were connected in parallel. The original script used to test the address of the ToF no longer worked with two sensors (unsure why, but might be that the script was not configured to handle more than one sensor) so instead, I used the read distance script again to make sure that, when I shutdown one of the sensors, the data received is from the other ToF sensor. As seen in the video below, the ToF sensor on the left has its shutdown pin connected to digital pin 4 on the Artemis. When I blocked the left sensor, the readings don't change. However, when I block the sensor on the right, the readings are affected.

<p align="left"><iframe width="720" height="408" src="https://youtube.com/embed/tutPGJ5YOR4"></iframe></p>
<p></p>

In order to read from both sensors, the method I chose was to change the address of one of the sensors. To do this, we need to download the [VL523L0X Arduino Library](http://robojax.com/node/1220){:target="_blank"} (from robojax.com) and include it in the header file. I followed [this tutorial](https://www.youtube.com/watch?v=RRQASevYK3g){:target="_blank"} to change the address of one of the ToF sensors (with the other sensor shutdown). I changed the address to 0x31 (but I can't print it since the original script to print the address doesn't work with more than one sensor).