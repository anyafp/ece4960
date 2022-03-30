---
title: "Lab 9: Mapping"
date: 2018-11-28T15:15:26+10:00
featured: true
weight: 9
layout: lab
---

<style type="text/css">
  .gist {width:700px !important;}
  .gist-file
  .gist-data {max-height: 500px;max-width: 700px;}
</style>

In this lab, we attemp to create a mapping of a room in the lab, and use this map in later localization and navigation tasks. In order to build the map, the robot was placed at several positions in the classroom and spun around its axis to caputre the ToF sensor readings.

# Lab Tasks

## PID Control

In a previous lab, I configured PID control for the front ToF sensor. In this lab, we need to use PID control for the IMU sensor readings instead. I decided to use the method that controlled angular speed, so my PID control looked like this:

<script src="https://gist.github.com/anyafp/62acd55fed8fe9ea79d44f6bf7823c7b.js"></script>

I ran it once. Not so nice plot :'(

<p align="left"><img src="../../images/lab9/Figure1.png" height="650" width="650"></p>