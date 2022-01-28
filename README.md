Cognitive AR Demo
=================

This repository contains all the code to run the [Cognitive AR](https://netidee.at/cognitivear) end-to-end demo.

## Overview

The setup includes the following steps

* Setting up the [edge node](https://github.com/cognitivexr/edge-node), although you can also run it entirely on your computer
* Setting up [CPOP](https://github.com/cognitivexr/cpop)
  * Calibrating the camera parameters
  * Anchoring the camera to the common origin
  * Start the MQTT Broker
  * Start the CPOP Server
* Setting up [CogStream](https://github.com/cognitivexr/cogstream)
* Setting up the [HoloLens app](https://github.com/cognitivexr/unity-demo-app)
  * Configuring IP addresses to components
  * Building and deploying to the hololens
* Start the Plattform
* Start the HoloLens App

## Seting up the edge node (or your computer)

TODO: install procuedure (or alternatively: pull the docker images)

## Seting up CPOP

### Calibrating the camera parameters

TODO: describe intrinsic camera calibration procedure

### Anchoring the camera to the common origin

TODO: describe anchoring using `anchor` CLI and the aurco pattern

## Start the Plattform

either use `docker-compose` or start services individually on your local machine

### Start the MQTT broker

### Start the CPOP Server

### Start the CogStream Mediator

## Start the HoloLens App
