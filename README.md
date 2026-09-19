# Autonomous Driving System in Webots

A simulated autonomous driving system developed in Webots using camera vision, LiDAR sensing, PID control, and traffic-aware decision logic.

The vehicle is designed to follow a road lane, avoid obstacles, slow down at zebra crossings, and stop when pedestrians or restricted-entry signs are detected.

## Project Overview

The project implements an autonomous vehicle in the Webots simulation environment.

The system combines camera and LiDAR data to allow the vehicle to navigate the environment while responding to different road conditions.

Main capabilities include:

- Yellow-line path following
- LiDAR-based obstacle avoidance
- PID steering control
- Zebra-crossing detection and slowdown
- Pedestrian detection and stopping
- Restricted-area recognition
- Automatic speed control
- Automatic steering control

## Zebra Crossing Detection

![Zebra Crossing](media/autonomous%20Project%20ppt%20%282%29.png)

The camera detects the yellow zebra-crossing markings in the simulated environment.

When a zebra crossing is detected, the vehicle reduces its target speed and applies braking before continuing once the crossing is clear.

```c
if (zebra_visible) {
    target_speed = 10.0;
    wbu_driver_set_brake_intensity(0.5);
}
```

## Pedestrian Detection

![Pedestrian Detection](media/autonomous%20Project%20ppt%20%281%29.png)

The camera recognition system checks for pedestrians in front of the vehicle.

The controller evaluates the pedestrian's horizontal position and apparent size in the camera image.

If the pedestrian is centered in the driving path and sufficiently close, the vehicle stops completely.

The vehicle resumes driving once the pedestrian is no longer detected.

## Restricted Area Detection

![Restricted Area](media/autonomous%20Project%20ppt.png)

The simulated environment also includes restricted areas marked using road signs.

The vehicle uses camera recognition to identify these road objects and respond to traffic restrictions in the environment.

## System Components

The main components used in the simulation are:

- Webots Simulator
- Camera sensor
- LiDAR sensor
- GPS
- Vehicle controller
- Webots object-recognition system

The camera is used for lane detection and object recognition, while the LiDAR is used to detect obstacles in front of the vehicle.

## System Workflow

The autonomous vehicle continuously performs the following process:

1. Capture the current camera image
2. Detect the yellow road line
3. Calculate the required steering correction
4. Read LiDAR distance measurements
5. Detect obstacles ahead
6. Adjust steering to avoid obstacles
7. Check for zebra crossings
8. Check for road signs and pedestrians
9. Calculate the target speed
10. Send steering and speed commands to the vehicle

## Lane Following

The camera is used to identify the yellow road line.

Pixels matching the reference yellow color are detected and their average horizontal position is calculated.

This position is converted into a line angle representing the vehicle's position relative to the lane.

The measured angle is filtered before being passed to the steering controller to reduce sudden changes.

```c
double yellow_line_angle =
    filter_angle(process_camera_image(camera_image));

line_steer = applyPID(yellow_line_angle);
```

## PID Steering Control

A PID controller is used to keep the vehicle aligned with the detected yellow line.

The controller uses three terms:

- Proportional
- Integral
- Derivative

The values used are:

```c
#define KP 0.25
#define KI 0.006
#define KD 2.0
```

The PID output determines the base steering correction required to keep the vehicle aligned with the road.

The final steering value can then be adjusted further when obstacle avoidance is required.

```c
final_steer = line_steer + avoid_bias;
```

## LiDAR Obstacle Avoidance

The LiDAR continuously scans the area in front of the vehicle.

A region around the center of the LiDAR scan is analyzed to determine whether an obstacle is within the configured detection distance.

If an obstacle is detected:

- The vehicle determines an avoidance direction
- An avoidance steering bias is calculated
- The vehicle slows down
- The avoidance bias is combined with the lane-following steering value

```c
obstacle_found =
    process_front_obstacle(
        sick_data,
        &obstacle_dist,
        &obstacle_offset
    );

final_steer = line_steer + avoid_bias;
target_speed = AVOID_SPEED;
```

When the obstacle disappears, the avoidance bias gradually decreases and the vehicle returns to normal lane following.

## Speed Control

Different target speeds are used depending on the current situation.

```c
#define CRUISE_SPEED 45.0
#define AVOID_SPEED 20.0
#define LOST_LINE_SPEED 16.0
```

The vehicle normally travels at cruising speed.

Its speed is reduced when:

- An obstacle is detected
- The yellow line is lost
- A zebra crossing is detected

The vehicle stops completely when required by the safety logic.

## Camera Recognition

The camera recognition system is used to detect objects in the simulated environment.

The controller can evaluate:

- Object type
- Horizontal position
- Apparent size in the image

This information allows the vehicle to decide whether an object is relevant to its current path and whether the vehicle should slow down or stop.

## Final Vehicle Control

After the perception and decision-making stages are complete, the final speed and steering values are sent to the simulated vehicle.

```c
set_speed(target_speed);
set_steering_angle(final_steer);
```

These commands combine the outputs from lane following, obstacle avoidance, and road-safety logic.

## Sensors

### Camera

The camera is used for:

- Yellow-line detection
- Zebra-crossing detection
- Pedestrian recognition
- Road-sign recognition

### LiDAR

The LiDAR is used to:

- Detect obstacles in front of the vehicle
- Estimate obstacle distance
- Estimate obstacle horizontal offset
- Generate an avoidance steering direction

### GPS

GPS information is used to obtain:

- Vehicle coordinates
- Vehicle speed

## Controller Logic

```text
Camera
   |
   v
Yellow Line Detection
   |
   v
PID Steering
   |
   +------------------+
                      |
LiDAR                 |
   |                  |
   v                  |
Obstacle Detection    |
   |                  |
   v                  |
Avoidance Bias -------+
                      |
                      v
                Final Steering

Camera Recognition
   |
   +--> Zebra Crossing --> Slow Down
   |
   +--> Pedestrian ------> Stop
   |
   +--> Road Signs ------> Safety Decision

                      |
                      v
               Vehicle Control
```

## Challenges

Several challenges were encountered during development.

### Simulator Compatibility

Some simulation environments originally considered for the project were not supported on the available devices.

### Project Interoperability

Simulation files did not always behave consistently across different computers, making collaboration and testing more difficult.

### Simulation Setup

Maintaining a stable autonomous-driving simulation required additional configuration and troubleshooting.

### Change in Project Scope

The project was initially intended to use a real autonomous vehicle but was adapted into a simulation-based implementation because of practical constraints.

## Technologies Used

- C
- Webots
- Webots Vehicle Driver API
- Camera Recognition API
- LiDAR API
- GPS API
- PID control
- Computer vision
- Autonomous navigation

## Live Demo

A demonstration of the autonomous vehicle operating in the Webots environment is available here:

[Watch the live project demo on YouTube](https://youtu.be/kqH6o-6FNpY)

The demo shows the vehicle navigating the simulated road environment while performing lane following, obstacle avoidance, and traffic-aware behaviors.

## Repository Structure

```text
webots-autonomous-driving-system/
│
├── README.md
├── src/
│   └── autonomous_controller.c
└── media/
    ├── autonomous Project ppt (2).png
    ├── autonomous Project ppt (1).png
    └── autonomous Project ppt.png
```

## About the Project

This project demonstrates the integration of perception, decision-making, and control within a simulated autonomous vehicle.

Camera vision is used for lane and environmental-object detection, LiDAR provides obstacle information, and a PID controller provides smooth lane-following steering.

These systems are combined with traffic-safety logic to allow the vehicle to respond dynamically to its simulated environment.
