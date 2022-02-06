---
title: "Lab 2: Bluetooth Communication"
date: 2018-11-18T12:33:46+10:00
featured: true
weight: 2
layout: lab
---

In this lab, we're going to establish communication between our computer and the Artemis board through the bluetooth stack. We'll be using Python inside a Jupyter notebook on the computer end, and the Arduinno programming language on the Artemis side.

# Setup

## BLE

Arduino has a library ([ArduinoBLE](https://www.arduino.cc/en/Reference/ArduinoBLE){:target="_blank"}) that supports all the Arduino boards that have the hardware enabled for Bluetooth Low Energy (BLE) and Bluetooth 4.0 and above. BLE is optimized for low power use at low data rates.

## Computer Setup

This is a brief summary of the computer set-up required for this lab.

__Install Python__
>
    $ python3 --version
    $ python3 -m pip --version

__Virtual Environment__
>
    $ python3 -m pip install --user virtualenv
    $ python3 -m venv ece4960_ble
    $ source ece4960_ble/bin/activate

__Python Packages__
>
    $ pip install numpy pyyaml colorama nest_asyncio bleak jupyterlab

## Artemis Board Setup

We installed __ArduinoBLE__ from the library manager in the Arduino IDE and loaded the ble_arduino.ino example sketch into the Artemis board. The Artemis board printed it's MAC address as shown below.

<p align="left"><img src="../../images/lab2/mac-add.png" height="600" width="600"></p>

# Code Overview

## Python Package

The `demo.ipynb` notebook is a demo file that helps us set up the bluetooth connection between the computer and the board.
<script src="https://gist.github.com/anyafp/ba2e30e060d7ff1b6c2c3657dba16224.js"></script>


<p align="left"><img src="../../images/lab2/two-ints.png" height="600" width="600"></p>

# Lab Tasks

## Echo

For this task, I had to update the Arduino sketch to handle the appropriate cases as instructed. In the case of the ECHO command type, the Artemis would need to echo the message that it had received back to the computer, where the computer would print it out. As instructed in the lab handout, I sent back the message to the computer but modifying it such that the computer receives `"Robot says -> <msg> :)"`.

The embedded code below shows the message printed in Jupyter Notebook and the screenshot of the Arduino serial output shows the print statement that shows when the Artemis sends the message to the computer.

## Send Three Floats

For this task, I also had to update the Arduino sketch. I referred to the `SEND_TWO_INTS` command and replicated it, changing the relevant parts of the code such that it took a float instead of an int, and took three sets of data instead of two. I then printed it to the serial output as shown in the screenshot below.

## Receive Float

In the demo, we had learned how to receive a float. In this task, we were still receiving a float. The difference is that, instead of having one instance of receiving a value, we would make a global variable that would constantly be updated using the notification feature that uses the `start_notify()` and `stop_notify()` functions. Whenever this global variable was printed, it would print the most updated value that is received. No Arduino code was needed to be added/modified, but there was some Python code that we needed to add.

The first step was to create the global variable that is to be updated. This was assigned a random value. The function `start_notify()` takes in two arguments: the uuid and a callback function. So the next step was to create the callback function. The callback function takes in two arguments: the uuid, which is purely for debugging purposes, i.e., to print in the callback function, and the bytearray to be converted into a float. This bytearray to be converted to a float (using the `bytearray_to_float` function) is then assigned to the global variable. Now that the callback function is complete, all we need to do is pass this function as the second argument in `start_notify()`, and the uuid (in our case, `'RX_FLOAT'`) as the first argument. After running this, I made a few cells that would print out the value of this global function. Without having to call `receive_float()`, this variable is constantly updated.



<script src="https://gist.github.com/anyafp/461692d263f434177b5cdbf4b2100b2c.js"></script>

<p align="left"><img src="../../images/lab2/arduino-lab-task.png" height="2000" width="2000"></p>