![image001](https://user-images.githubusercontent.com/23239868/28748600-b190782c-7489-11e7-82ae-55db54c94050.jpg)
# Autonomous Snowmobile - General User Interface (GUI)/Ground Control Station (GCS) #

*The Pennsylvania State University - School of Electrical Engineering and Computer Science*

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

[MakerHub LINX](http://sine.ni.com/nips/cds/view/p/lang/en/nid/212478)


## Main Data Flow ##

The data flow diagram below describes the main control interface for the SnowDrone (this excludes states such as: openning .kml files, disabling controls, temperature settings, configuration/settings, etc). This diagram should give the user a general overview about how the SnowDrone operates and where more states can be added. For example, if a gyroscope were to be added it could be placed in the **Check Status** state - effectively adding a new message to the queue which checks the gyroscope values.

![dataflow](https://user-images.githubusercontent.com/23239868/28581022-23d36592-712f-11e7-9085-d6b1512bad03.jpg)

## Documentation on Completed Subsystems and Algorithms ##
The subsystems that have been successfully completed are the following: *GPS and Google Earth integration, actuator and steering control, throttle control, temperature and fan control*. A short description and visuals for each completed subsystem are included below.

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

### Throttle Control ###

Please see: [Throttle Control Respositroy](https://github.com/rrichards7/Throttle-Control)

### Actuator Control and Steering Algorithm ###

Please see: [Actuator Control Respository](https://github.com/rrichards7/Actuator-Control)
  
The steering/navigating algorithmn itself is not explained in the actuator control repository. The algorithm is simple and does not require a compass. Rather, GPS points are read in ~2/second. Simple computations are made which calculate two things: (1) current bearing and (2) bearing to desintation. The steering angle of the snowmobile is then set to the bearing (angle) required to reach the destination. If the bearing exceeds +/-30 degrees then the steering angle is set to +/- 30 degrees respectively. 

The actuator is moved for a certain amount of time (following the code in the actuator control repository) to make the snowmobile steer. The actuator moves a certain length every second. From there, the steering angle can be determined empirically from the amount of time the actuator is active. For example, if the actuator it actively extending for 20 seconds, then this equates to a 25 degree turn.

The actuator's operation manual specifically states different weaknesses, namely the thermal regulation. The actuator has a specific active duty cycle, which means every time it is active, it has a calculatable required rest time. This thermal regulation was coded and included in the GCS VI.

### Camera and Obstacle Avoidance ###

Please see: [Obstacle Avoidance (Color Threshold)](https://github.com/rrichards7/Obstacle-Avoidance-CT) and [Obstacle Avoidance (Curl-Field Analysis)](https://github.com/rrichards7/Obstacle-Avoidance-OF)

### GPS Control ###

Please see: [GPS Control Repository](https://github.com/rrichards7/GPS-Control)

## Download and Setup ##

To download:

(1) click **Clone or download** >> **Download ZIP**

![1](https://user-images.githubusercontent.com/23239868/29080006-b3350dbe-7c2c-11e7-930e-f8a9a518585e.JPG)

(2) **Unzip** or **Extract** the folder and open the **SnowDroneProj.lvproj** file

(3) open the **Main.vi** VI within the project (this is the GUI)

![2](https://user-images.githubusercontent.com/23239868/29080463-d043f586-7c2d-11e7-8dfc-08150bcaa3c9.JPG)

## Operation Instructions ##

To operate:

(1) follow the instructions in the **GPS and Google Earth API** section to make a .kml file

(2) **Run** the Main.vi VI

(3) click the **Open Path** button to upload the .kml file

![3](https://user-images.githubusercontent.com/23239868/29080751-9fcd531a-7c2e-11e7-9a13-a232870c237b.JPG)

**Note:** The program will automatically parse the .kml file and indicate in the text field whether or not the .kml read was successful

![4](https://user-images.githubusercontent.com/23239868/29080750-9fc975e2-7c2e-11e7-8245-9cdbf9b14e68.JPG)

(4) If all subsystems are properly connected and the pins are configured correctly, click the **Start** button

**Note:** The program will take over and start the feedback loop illustrated in the **Main Data Flow** section

(5) to stop/abort, press the **Abort** button

## Notes and Future Considerations ##

This section includes a list of notes about the code and recommended modifications for future groups:

(1) The configuration botton was left with no function - however, thoughts on what would belong in a configuration panel are included in the code.

(2) The pin numbers for the Arduino were randomly considered and placed in the LabVIEW code convienietly in each case. However, it would be incredibly useful to have a pop-up window/dialog (configuration panel) where the user could input the pins for each subsystem so that there is more organization. These pin values could be written to a global variable and read in future cases when needed.

(3) The actuator steering control system might want to be revised. Rather than turning the exact number of degrees every time, it may be more useful to turn in single degree increments (essentially a PID system).

(4) The camera code is essentially complete but must be fully integrated in the GUI/GCS. The camera code must be modified to send a custom command to the LabVIEW code which triggers some avoidance maneuver (described more in detail in the [Obstacle Avoidance (Color Threshold Repository)](https://github.com/rrichards7/Obstacle-Avoidance-CT)

(5) The radios did not function properly and therefore were not integrated. Future teams must use the XTend or XBee radios to make the ArduinoMEGA *"mobile"*.

## Appendix ##

If you have any questions about this code or related subsystems, contact me via email with subject: **SnowDrone LabVIEW**

**email:** *ryan.richards2007@gmail.com*
