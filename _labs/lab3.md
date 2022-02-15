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

Next, we tested the ToF sensor using the SparkFun readDistance example code. I measured the distance of a sensor against a ruler to compare the actual vs measured distance in increments of 2.5cm up to 30cm. Below is the graph of the results.

<p align="left"><img src="../../images/lab3/tof-dist.png" height="600" width="600"></p>
<p align="left"><img src="../../images/lab3/tof-graph.png" height="400" width="400"></p>

When testing the distance with different variables, we noticed that the ToF sensor does not work very well with clear objects. It would detect the object with very poor accuracy.

<p align="left"><img src="../../images/lab3/tof-clear.png" height="600" width="600"></p>

### 2 ToF Sensors in Parallel

In order to read from both sensors, the method I chose was to change the address of one of the sensors. To do this, we first need to initialize two different sensors globally. In the setup, we shutdown one of the sensors, and change the address of one of the sensors to 0x32 (unsure why this is, but other address values that we've tried do not work; I think I saw 0x32 on some forum and used it and it works). Then we turn the other sensor back on. Below is a screenshot of the serial output of both sensor readings with the position below.

<p align="left"><img src="../../images/lab3/serial-2tofs.png" height="600" width="600"></p>
<p align="left"><img src="../../images/lab3/img-2tofs.png" height="400" width="400"></p>

## IMU

### Setup

We ran the basic IMU example on Arduino and printed the results in the serial monitor as shown below. The AD0_VAL should be set to 0 because this is the value of the last bit of the I2C address. Since the ADR jumper is closed, this value should be 0.

The screenshot below is the serial plotter data of the example script. The first half of the graph is when I tilted the IMU in all 4 directions. The second half of the graph is when I accelerated the IMU back and forth.

<p align="left"><img src="../../images/lab3/imu-graph.png" height="800" width="800"></p>

The accelerometer data changes when I accelerated the IMU back and forth, and the gyroscope data changes when I tilt the IMU in different directions.

### Accelerometer

