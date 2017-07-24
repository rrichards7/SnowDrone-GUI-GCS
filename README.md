# Autonomous Snowmobile - SnowDrone 
LabVIEW Code for General User Interface (GUI)/Ground Control Station (GCS)

![1](https://user-images.githubusercontent.com/23239868/28534685-84859baa-706f-11e7-8227-601510b2cf22.PNG)

## Introduction

A LabVIEW project was constructed in order to unify the subsystems on the SnowDrone. A specific producer/consumer event driven architecture was chosen to facilitate the addition of more subsystems. This readme file explains to future students how the LabVIEW project operates, how to interface with and add more components, hardware considerations, as well as system architecture. 

## GUI/GCS Architecture
Before subsystems are described in futher detail, it is important to understand the architecture chosen. This code is structured around events and message chains - i.e. when the user presses a button then an event will be triggered and something will happen, or a chain of events will happen. The best and most efficient architectre to accomodate this system is a message-queue based producer/consumer loop (shown in Figure 1).

![maind](https://user-images.githubusercontent.com/23239868/28534990-ccb4d994-7070-11e7-8b19-d2794b14af3e.png)

*Figure 1 - LabVIEW Block Diagram*

The producer loop is based on an Event Structure, which is programmed to read changes in control values. Therefore each control (button) on the front panel has its own case (along with other user-defined events) in the Event Struture (shown in Figure 2 below)

![image](https://user-images.githubusercontent.com/23239868/28535187-6ddb5442-7071-11e7-8bca-766168413442.png)

*Figure 2 - All producer loop event cases*

For instance, if the user clicks the "Open Path" button then the event will register in the producer loop and the appropriate message will be added to the queue - in this case "Open Path" (shown in Figure 3).

![maind1](https://user-images.githubusercontent.com/23239868/28535541-c0b11e58-7072-11e7-8f27-6f6470004666.png)

*Figure 3 - Open Path producer case*

Once the message is enqueued, the message is sent to the consumer loop, where it is fed into the case selector of the case structure. This means that if an "Open Path" message is sent, then the "Open Path" case will be selected in the case stucture of the consumer loop (shown below in Figure 4).

![maind28](https://user-images.githubusercontent.com/23239868/28536017-5fd45c74-7074-11e7-8202-1c39a3aeda5c.png)

*Figure 4 - Open Path consumer case*

Therefore the consumer loop executes code to complete the action associated with the message. So in this case, if the user wants to open a path:
(1) the "Open Path" button is pressed
(2) the button-press action is registered by the event structure
(3) the "Open Path" message is enqueued and sent to the consumer loop
(4) the dialog to open a path, etc is executed









## Completed Subsystems
The subsystems that have been successfully integrated are the following: GPS and Google Earth integration, Actuator, Throttle control, Temperature and fan control. A short description and visuals for each completed subsystem are described below.

### GPS and Google Earth
The previously proposed system that handles GPS waypoint 

### Actuator

### Throttle Control

### Temperature Monitor/Fan Control



## Download and Setup

## Future Work

future work blurb here...
...
...

### Camera
camera subsystem will rely on optical flow method

### Actuator and Motor Control 

PWM board for actuator

### Throttle Control

correct setup

### XTend Radios
