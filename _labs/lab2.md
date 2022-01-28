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
> 
    $ python3 --version
    $ python3 -m pip --version

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