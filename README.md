Cognitive AR Demo
=================

This repository contains all the code to run the [Cognitive AR](https://netidee.at/cognitivear) end-to-end demo.

The following figure shows a rough overview of what we will set up:
<p align="center">
  <img src="https://raw.githubusercontent.com/cognitivexr/cognitivear-demo/main/images/cognitivexr-end-to-end.png" alt="End-to-end demo">
</p>


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
* Start the Platform
* Start the HoloLens App

## Seting up the edge node (or your computer)

CognitiveXR is built to run on GPU-accelerated edge computing hardware, such as NVIDIA Jetson devices.
Our [edge-node](https://github.com/cognitivexr/edge-node) documentation gives instructions on how to build your own edge cluster.

If you want to run the platform on your computer you need:
* Docker (for building and running the platform components)
* Python (for calibrating CPOP)
* A webcam (for CPOP)
* (optional) An NVIDIA GPU with CUDA 11 and the [NVIDIA Docker Toolkit](https://github.com/NVIDIA/nvidia-docker)

## Seting up CPOP

### Calibrating the camera parameters

To calibrate the intrinsic parameters of the camera that will be used for CPOP, please follow these steps: https://github.com/cognitivexr/cpop#intrinsic-camera-parameters.
For this you will need to run CPOP on your host machine, as you require a working OpenCV UI to validate the camera calibration.
The generated files (e.g., `default__intrinsic_1024x576.json`) will be placed into `~/.cpop/cameras` and should be copied into this repository to `etc/cpop/cameras`.

Here is a video showing the procedure.
The more angles and good frames you capture, the better the result will be.

https://user-images.githubusercontent.com/3996682/151710294-662f904d-69c1-4c1b-b5b1-9182035c0319.mp4

### Anchoring the camera to the common origin

Once the intrinsic camera parameters are determined, we can now anchor the camera to a common origin.
The exact steps are described here: https://github.com/cognitivexr/cpop#extrinsic-camera-parameters

First, place the camera where it should perceive the environment from.
Print the ArUco marker and put it on the ground where it is visible to the camera.
After anchoring, it should not be moved, otherwise the coordinates sent to other devices will be wrong.
use the `python -m cpop.cli.anchor` command to run the anchoring.
Copy the `default__extrinsic.json` file from `~/.cpop/cameras` into `etc/cpop/cameras`.

Here is what the procedure looks like (marker is on the table for demonstration purposes).

https://user-images.githubusercontent.com/3996682/151709934-9aa15686-abd1-4e64-993b-c6efc5f3a5b7.mp4

The camera is now calibrated and ready to be used in the CPOP server!

## Start the platform

Either use `docker-compose up`, or start services individually.

### Start platform with docker-compose

The `docker-compose.yml` contains a basic docker-compose that starts up all system components.
The `.env` file contains relevant parameters.
The `etc/` folder contains several configuration files that are mounted into the system components.

#### Build the container images

We currently don't host pre-built docker images, so you will have to build them yourself:
* CPOP
* CogStream Mediator
* CogStream Engines (e.g., fermx, and yolov5)

You can find build instructions for the docker images in the respective repositories.

#### Set correct engine image

in `etc/engines/` you can find the engine specifications that describe how engines are started by the mediator.
If you wish to use the CPU images for the fermx or yolov5 engine, then you can change the tag from `cuda-110` to `cpu`.
https://github.com/cognitivexr/cognitivear-demo/blob/e98ab951e219132f1b68c9331a6adb923174a9ca/etc/engines/fermx.spec.json#L5

#### System parameters

| Variable | Description |
| -------- | ----------- |
| CPOP_CAMERA_DEVICE | The camera device of the host (e.g., `/dev/video0` for the default camera) |
| CPOP_CAMERA_MODEL | The camera model (e.g., `c920`) to reference a particular set of camera parameters in `etc/cpop/cameras` |

### Start platform components individually

#### Start the MQTT broker

You can use and start any MQTT-compliant broker.
The MQTT broker port and host needs to be configured when
1. starting CPOP (via the environment varibale `CPOP_BROKER_PORT` and `CPOP_BROKER_HOST`).
2. building the HoloLens app (TODO: point to code)

#### Start the CPOP Server

See [the CPOP documentation](https://github.com/cognitivexr/cpop#run-the-service) on how to run the CPOP service manually.

#### Start the CogStream Mediator

See [the CogStream documentation](https://github.com/cognitivexr/CogStream) on how to build and run the mediator.

## Start the HoloLens App

Our [Unity Demo App](https://github.com/cognitivexr/unity-demo-app/) can be built for and deployed on the HoloLens 2.
It contains two scenes that demonstrate the integration of the HoloLens with the CognitiveXR platform.

### App 1: Indoor environmental sensor tracking

This app demonstrates the integration with CPOP.
The AR device doesn't do any tracking, it only receives sensor data from the environment in real time that CPOP collects.

https://user-images.githubusercontent.com/3996682/151723092-653bf867-7064-4fb1-9358-c2c17703fa64.mp4


### App 2: Video offloading for facial expression recognition

Once the HoloLens app is deployed, you can select the fermx engine in the app.
The HoloLens will connect to the mediator that creates a new analytics engine, which will then process the video feed.

https://user-images.githubusercontent.com/3996682/151723007-1eb70c9f-eeb1-4c1f-85d1-57a0fafc2cc6.mp4


