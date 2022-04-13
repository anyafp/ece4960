---
title: "Lab 10: Simulator"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 10
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

In this lab, set up and used our simulator environment. One of the reasons why we might use a simulator is because the environment is more predictable and we can test out potential mistakes that might lead to our car getting destroyed.

# Prelab

Just like in Lab 2, there was a bit of setup to do to ensure that the simulator works properly.

__Upgrade Python__
>
    $ python3 --version
    $ python3 -m pip --version

Since we're using a new upgraded version of Python, we need to repeat the steps in Lab 2 to ensure that all the Python dependencies are installed and that the virtual environment is set up properly.

__Install pip Packages__
>
    $ python3 -m pip install numpy pygame pyqt5 pyqtgraph pyyaml ipywidgets colorama

__Install Box2D Package__
>
    $ source ece4960_ble/bin/activate
    # Download pip wheels*
    $ pip install --find-links <wheel_directory> box2d
    $ python
    >> import Box2D
    >> print(Box2D.__version__)
    2.3.10

\* [pip wheels](https://cornell.box.com/s/3ihhcv6efjfhdkkmtwn0qktj1zd1pxc7){:target="_blank"}

__Prepare Simulator__

1. Download and extract the [simulation base code](https://github.com/CEI-lab/ECE4960-sim-release){:target="_blank"} into the project folder
2. Downaload the [Lab 10 Notebook](https://github.com/CEI-lab/ECE4960-lab10){:target="_blank"} and copy lab10.ipynb into the notebooks directory (inside the simulation base code directory)
3. Follow the instructions in the notebook to understand how to control your virtual robot and use the plotter

# Lab Tasks

## Simulator Features and Functionalities

### Starting and Stopping the Simulator

In order to start and stop the simulator, there are two options: using the GUI, or doing it using functions. 

__GUI__
<p align="left"><img src="../../images/lab10/gui.png" height="300" width="300"></p>

__Functions__
>
    START_SIM()
    STOP_SIM()
    RESET_SIM()
>
    START_PLOTTER()
    STOP_PLOTTER()
    RESET_PLOTTER()

### Interactions

There are also two methods of interacting with the simulator and plotter: using a mouse and keyboard, or using the commander class.

__Mouse and Keyboard__

Simulator:
* Press the up and down arrow keys to increase and decrease the linear velocity of the virtual robot, respectively.
* Press the left and right arrow keys to increase to increase and decrease the angular velocity of the virtual robot, respectively.
* Hit spacebar to stop the robot.
* Press h to display the full keyboard map.

Plotter:
* Use the GUI buttons to show/hide the various plots.
* Use your mouse to zoom in and out of the plot.
* Press the "A" button (bottom-left to the plotting box and above "Plotted Points") to auto-fit the plot to your window.

<br>

__Commander Class__

<table align="left">
    <tr>
        <th style="text-align: left; font-size: medium">Member Functions</th>
        <th style="text-align: left; font-size: medium">Description</th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="font-family:monospace">Utility Functions</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">sim_is_running()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Get the run status of the simulator.</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">plotter_is_running()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Get the run status of the plotter.</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="font-family:monospace">Plotter Functions</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">plot_odom(x,y)</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Plot a point (x,y) in the plotter in red. Units are (meters, meters).</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">plot_gt(x,y)</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Plot a point (x,y) in the plotter in green. Units are (meters, meters).</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">plot_bel(x,y)</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Plot a point (x,y) in the plotter in blue. Units are (meters, meters).</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">plot_map()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Plot the map based on the map lines in <em>world.yaml</em>.</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">reset_plotter()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Reset the plots in the plotter.</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="font-family:monospace">Simulator Functions</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">set_vel(linear_vel, angular_vel)</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Set the linear velocity (m/s) and angular velocity (rad/s) of the virtual robot.</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">get_pose()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Get the odometry and ground truth poses of the virtual robot as two numpy arrays. The units of each pose are (meters, meters, radians)</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">get_sensor()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Get the ToF sensor data (in meters) of the virtual robot as a numpy column array.</span></th>
    </tr>
    <tr>
        <th style="text-align: left"><span style="color:rgb(201,152,4);font-family:monospace">reset_sim()</span></th>
        <th style="text-align: left"><span style="font-weight: normal">Reset the virtual robot to its initial pose.</span></th>
    </tr>
</table>

## Open Loop Control