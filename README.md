# Autonomous Snowmobile - SnowDrone 
LabVIEW code for the General User Interface (GUI)/Ground Control Station (GCS)

The Pennsylvania State University - School of Electrical Engineering and Computer Science

![1](https://user-images.githubusercontent.com/23239868/28534685-84859baa-706f-11e7-8227-601510b2cf22.PNG)

## Introduction ##

A LabVIEW project was constructed in order to unify the subsystems on the SnowDrone. A specific producer/consumer event driven architecture was chosen to facilitate the addition of more subsystems. This readme file explains to future students how the LabVIEW project operates, how to interface with and add more components, hardware considerations, as well as system architecture.

## GUI/GCS Architecture ##
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

## MakerHub LINX ## 
LINX is a library developed by MakerHub that allows programmers to interface with the RaspberryPi, Arduino and other microcontrollers on LabVIEW. LINX has been used extensively in this project to communicate with the ArduinoMEGA, which controls each subsystem. Download and other information can be found at:

http://sine.ni.com/nips/cds/view/p/lang/en/nid/212478

## Main Data Flow ##


## Completed Subsystems and Algorithms ##
The subsystems that have been successfully completed are the following: *GPS and Google Earth integration, Actuator and Steering control, Throttle control, Temperature and fan control*. A short description and visuals for each completed subsystem are described below.

### GPS and Google Earth API ###
The previously proposed control system that handled GPS waypoints was outdated and hard to use. The newly designed and fully integrated system uses the Google Earth API (application programming interface) to plot waypoints, paths, areas and see pings of the snowmobile's current location.

#### Entering Waypoints and Creating a .kml file ####

Rather than creating a LabVIEW-based program that allows the user to enter GPS coordinates, it can be completed in Google Earth. Once Google Earth is opened, right click **My Places** and click **Add** >> **Folder**

![1](https://user-images.githubusercontent.com/23239868/28537036-85cc89c6-7077-11e7-9634-c169b6df7455.PNG)

From here, dialog will come up asking the user futher information about the new folder. Once the user is done, simply select **OK**.

![2](https://user-images.githubusercontent.com/23239868/28537392-af1366b4-7078-11e7-809d-5661b25fcf94.PNG)


After ensuring that the desired folder is clicked/highlughted, press the **Add Placemark** button 
![3](https://user-images.githubusercontent.com/23239868/28537448-e0f6ccca-7078-11e7-9b3b-4913c13ff84c.PNG)

This will allow the user to enter the appropriate information about that placemark/waypoint, including specific lattitude/longitude coordinates, attributes, etc. Once the user is done, simply click **OK**.

![4](https://user-images.githubusercontent.com/23239868/28537516-286868a2-7079-11e7-999f-8063d0e69d27.PNG)

We can see now that the point has been added to the correct folder.

![5](https://user-images.githubusercontent.com/23239868/28537585-6a2cac6c-7079-11e7-88a8-41de9f92e020.PNG)

Finally, to properly save the file for LabVIEW, rightclick on the folder that you want to save and press **Save Place As...**

![6](https://user-images.githubusercontent.com/23239868/28537767-02687eca-707a-11e7-8f95-43cc22052b82.PNG)

Then navigate to the desired location and save the file as **.kml** 

![7](https://user-images.githubusercontent.com/23239868/28537815-26e53450-707a-11e7-805c-158e1e745eb2.PNG)

#### Opening a .kml file in LabVIEW and Displaying Waypoints ####

When the user runs the main LabVIEW program, Google Earth should automatically open and display itself on the LabVIEW front panel. The same route that was configured in Google Earth should be dispalyed in LabVIEW.

![8](https://user-images.githubusercontent.com/23239868/28538224-79aab1f0-707b-11e7-9d30-984b40a99b09.PNG)

From there, the user can click the "Open Path" button. Dialog will then appear - prompting the user to select the appropriate .kml file. Once it is chosen, click **OK**. The file will be automatically read and parsed - effectively extracting the lattitude/longitude corrdinates for each waypoint.

![9](https://user-images.githubusercontent.com/23239868/28538246-9c0d483e-707b-11e7-809e-42091e790e0e.PNG)

#### Displaying Current Location ####

Once the GPS is connected to the Arduino MEGA, the GPS point (lattitude/longitude) will be read from the GPS device and extracted through LabVIEW. LabVIEW will then programmatically write a .kml file called **"CurrentLocation.kml"** which createes a new point to display on the Google Earth map. Since constant GPS points are taken when the program is running, the .kml file is constantly rewritten with new values and read back into Google Earth and displayed.

### Actuator Control and Steering Algorithm ###

The actuator is controlled by the Arduino via a motor shield to turn the vehicle. The actuator that was ordered does not have onboard PWM control. A relay cannot be used, as the actuator must be actively extended/retracted. Therefore the best way to control the actuator is with a motor shield for the arduino. This allows the actuator to be controlled by PWM and does not deteriorate any of the components. The motor shield used for testing has a 2A limit, while the actuator requires a maximum current draw of ~8-10A. Therefore a motor shield with a higher current rating is required (but for basic testing purposes the lower current rated shield will work).

<pictures>
  
The steering algorithm is simple and does not require a compass. Rather, GPS points are read in ~2/second. Simple computations are made which calculate two things: (1) current bearing and (2) bearing to desintation. 

<pictures>
  



### Throttle Control ###

CAD Files and design

testing and code



### Temperature Monitor/Fan Control ###

A thermometer was added to check the ambient temperature in the control box. 


## Download and Setup

make sure all dependencies are here
project download
instructions for making project into .exe or creating installer


## Future Work

future work blurb here...
...
...

### Camera and Obstacle Avoidance ###



### Actuator Motor Shield ### 

PWM board for actuator

### Throttle Control ###

correct setup

### XTend Radios ###

setup
