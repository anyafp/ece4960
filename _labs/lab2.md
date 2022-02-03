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

## Artemis Package

- insert description of files -

## Python Package

The `demo.ipynb` notebook is a demo file that helps us set up the bluetooth connection between the computer and the board.

### Import Necessary Modules

>
    %load_ext autoreload
    %autoreload 2
>
    from ble import get_ble_controller
    from base_ble import LOG
    from cmd_types import CMD
    import time
    import numpy as np
>
    LOG.propagate = False

### Configuration

Before connecting the board and computer, it's important to change the parameters in the file `connection.yaml` to match the Artemis address with the one that was printed in the Arduino serial output window. This is so that the computer looks for the correct board.
>
    artemis_address: 'C0:07:85:8D:B8:44'

Then we can go ahead and create the ArtemisBLEController object (defined in `ble.py`) and connect to the device:
>
    ble = get_ble_controller()
    ble.connect()

<p></p>

I had some trouble connecting the computer to the Artemis device despite having matched the artemis address in the `connection.yaml` file. The solution to this was found in the course staff's [FAQ page](https://cei-lab.github.io/ECE4960-2022/tutorials/FAQ.html){:target="_blank"} where there was a connection issue in macOS12.
Below was the initial output of the Python package.

<p align="left"><img src="../../images/lab2/config-fail.png" height="2000" width="2000"></p>

And once the issue was resolved, the successful connection outputs displayed, as shown below.
<p align="left"><img src="../../images/lab2/config.png" height="2000" width="2000"></p>
<p align="left"><img src="../../images/lab2/connect.png" height="2000" width="2000"></p>

### Receive Data from the Artemis Board
>
    # Read a float GATT Charactersistic
    f = ble.receive_float(ble.uuid['RX_FLOAT'])
    print(f)

Output: `807.5`
>
    # Read a string GATT Charactersistic
    s = ble.receive_string(ble.uuid['RX_STRING'])
    print(s)

Output: `[->9.0<-]`

### Send a Command to the Artemis Board
>
    ble.send_command(CMD.PING, "")
    s = ble.receive_string(ble.uuid['RX_STRING'])
    print(s)

Output: `PONG`
>
    ble.send_command(CMD.SEND_TWO_INTS, "2|-6")
<p align="left"><img src="../../images/lab2/two-ints.png" height="2000" width="2000"></p>

### Disconnect
>
    ble.disconnect()

# Lab Tasks
