---
title: "Lab 13: Path Planning and Execution"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 13
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

Now that we've successfully completed Labs 1-12, we are finally at the point where we want to do path planning and execution using anything that we've learned in the past 12 labs. We have to get our robot to hit the waypoints listed below as fast and accurately as possible.

1. (-4, -3) [start]
2. (-2, -1)
3. (1, -1)
4. (2, -3)
5. (5, -3)
6. (5, -2)
7. (5, 3)
8. (0, 3)
9. (0, 0) [end]

<p align="left"><img src="../../images/lab13/path.png" height="400" width="400"></p>

# Implementation Details

Below is a simple overview of how the robot is going to hit the waypoints:

1. The robot has a target state it wants to achieve.
2. It performs localization to find out its current belief state.
3. Depending on its current belief state, it will execute some actions in order to try to get to the target state. This repeats until the robot reaches the target state (or somewhere near there).
4. Once it reaches the target state, the next waypoint becomes the robot's new target state.

## Target States

Since the waypoints are only the physical coordinates of the robot, I wanted to find the actual state that it would be in. Since the state also includes the angle of the robot, I set the target angle to be the one that faces the next waypoint.

| Waypoint (ft) | Waypoint (m) | Angle (º) | State |
| -------- | ----- | ------- | 
| (-4, -3) | (-1.2192, -0.9144) | 45º | 
| (-2, -1) | (-0.6096, -0.3048) |  0º |
| ( 1, -1) | ( 0.3048, -0.3048) | 300º |
| ( 2, -3) | ( 0.3048, -0.9144) | 0º |
| ( 5, -3) | (  1.524, -0.9144) | 90º |
| ( 5, -2) | (  1.524, -0.6096) | 90º |
| ( 5,  3) | (  1.524,  0.9144) | 180º |
| ( 0,  3) | (      0,  0.9144) | 270º |
| ( 0,  0) | (      0,       0) | anything |

# Acknowledgments
