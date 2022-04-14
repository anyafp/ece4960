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

__Commander Class__

<p align="left"><img src="../../images/lab10/cmdr-func.png" height="`1500`" width="1500"></p>

## Open Loop Control

For this portion of the lab, we needed to use the simulator to execute a "square" loop in the map. I used the function ``cmdr.set_vel()`` to set the linear and angular velocity. I fixed the amount of time that the simulator should stay "moving forward" or "turning" and used trial and error to find the linear and angular velocities that would work with this amount of time. Eventually, I settled on the values below.

<script src="https://gist.github.com/anyafp/0e48e87c398af8b8a7b78a2cffd07639.js"></script>

<p align="left"><iframe width="720" height="265" src="https://youtube.com/embed/FHJDf628y7k"></iframe></p>
<p></p>

The plotter plots two graphs. The green plot represets the __ground truth__, which is the actual position of the robot in the simulator. The red plot represents the __odometry__ data, which is the data from onboard sensors. In this simulated case, the IMU sensor data is being mimiced. The red and green plots should theoretically be the same, but the odometry data takes into account noise and other external factors, which explains why the readings are very off.

When moving forward, using a linear velocity of 0.5 m/s (as seen in ``cmdr.set_vel(0.5,0)``), I set the simulator to run the car forward for 1s (as seen when I use the ``asyncio.sleep(1)``). When turning, using an angular velocity of 1.55 rad/s (as seen in ``cmdr.set_vel(0,1.55)``), I set the simulator to turn the car for 1s (as seen when I use the ``asyncio.sleep(1)``). The robot executes the same shape every time as long as I reset the robot before every run since this is open loop control and there is minimal noise.

However, if I don't reset the robot and run the square loop several times, I obtain this ground truth because my square loop is not an exact square.

<p align="left"><img src="../../images/lab10/gt-many.png" height="500" width="500"></p>

## Closed Loop Control

For the closed loop portion of this lab, we needed to design a controller such that the car avoids obstacles. This is relatively simple since we have the function ``get_sensor()`` that returns the front ToF sensor data of the car. I utilized this to make the car turn when the ToF sensor data is smaller than a certain value (0.35m) and randomized the linear and angular velocities.

<script src="https://gist.github.com/anyafp/2e5085c8f59418f153246edb8c07ef43.js"></script>

<p align="left"><iframe width="720" height="265" src="https://youtube.com/embed/KQxTn15kqsA"></iframe></p>
<p></p>

__By how much should the virtual robot turn when it is close to an obstacle?__

Since the ToF sensor data doesn't give us much information (like the angle we're approaching the obstacle at) other than the raw distance to the obstacle, we are not able to give a good value for the car to rotate. Hence, I just gave a random value for the angular velocity ranging from 1 rad/s to 3 rad/s and the car would turn for a duration of 1s every time as in the video above.

__At what linear speed should the virtual robot move to minimize/prevent collisions? Can you make it go faster?__

Starting with a velocity of 0.5m/s, and the robot had no trouble avoiding the obstacles (except for a special case described later). This speed prevented any collisions even when the car came at the wall at an angle. So I pushed the speed from 3m/s to 5m/s as seen in the video below. For most of the near collisions, when going at 3m/s, the car barely touches the wall and only collides with the wall a few times. When we get to 5m/s, the car collides with the wall most of the times. These runs were all done with a distance of 0.35m given as the limit for the ToF sensor.

<p align="left"><iframe width="720" height="265" src="https://youtube.com/embed/VbiqOPX_IAE"></iframe></p>
<p></p>

__How close can the virtual robot get to an obstacle without colliding?__

Fixing the linear velocity to 1m/s, I varied the distance the ToF sensor has to detect for it to turn. Since I started with 0.35m, i decremented it by 10cm. With a distance of 0.35m, the car does not collide with obstacles 90% of the time while it collides with obstacles 50% of the time when the distance is set to 0.25m. When I set it to 0.15m, it hits obstacles most of the time. However, these collisions were determined visually which may be inaccurate. Also, the linear velocity plays a big role as to how close the car can get.

<p align="left"><iframe width="720" height="265" src="https://youtube.com/embed/GYfVlv3BwUc"></iframe></p>
<p></p>

__Does your obstacle avoidance code always work? If not, what can you do to minimize crashes or (may be) prevent them completely?__

No it does not. There are two major cases in which the obstacle avoidance does not work. One is when the car comes at an angle, and the corner of the car collides with the wall.

<p align="left"><img src="../../images/lab10/prob1.jpeg" height="300" width="300"></p>

The other major case is similar, but the corner of the car collides with the obstacle instead of the wall.

<p align="left"><img src="../../images/lab10/prob2.jpeg" height="300" width="300"></p>

To solve these problems, we could have two ToF sensors attached to the car at the front, one that's on the far left and one that's on the far right to account for the front corners of the car.