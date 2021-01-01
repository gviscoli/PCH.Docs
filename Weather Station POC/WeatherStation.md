# Weather Station Proof of Concept
Welcome to the open-source of my proof of concept for implentation of a basic Weather Station.


## Purpose
The goal of this poc is to experiment with a series of cloud technologies such as **Azure IoT Hub**, **Azure Function** and **Azure SignalR**.


### Azure IoT Hub
IoT Hub is a managed service, hosted in the cloud, that acts as a central message hub for bi-directional communication between your IoT application and the devices it manages. You can use Azure IoT Hub to build IoT solutions with reliable and secure communications between millions of IoT devices and a cloud-hosted solution backend. You can connect virtually any device to IoT Hub.

IoT Hub supports communications both from the device to the cloud and from the cloud to the device. IoT Hub supports multiple messaging patterns such as device-to-cloud telemetry, file upload from devices, and request-reply methods to control your devices from the cloud. IoT Hub monitoring helps you maintain the health of your solution by tracking events such as device creation, device failures, and device connections.


### Azure Function
Azure Functions is a serverless solution that allows you to write less code, maintain less infrastructure, and save on costs. Instead of worrying about deploying and maintaining servers, the cloud infrastructure provides all the up-to-date servers needed to keep your applications running.

### Azure SignalR
Azure SignalR Service simplifies the process of adding real-time web functionality to applications over HTTP. This real-time functionality allows the service to push content updates to connected clients, such as a single page web or mobile application. As a result, clients are updated without the need to poll the server, or submit new HTTP requests for updates.


## Weather Station PoC (Sketch)
Before starting the realization I defined which components should be the basis of the POC. To facilitate the decision I made a drawing of the final architecture:

In the followig figure, I am showing the sketch of Weather Station PoC.

![Blueprint PoC](Images/WeatherStationHA.png "Blueprint PoC")


## Solution Architecture
In the followig figure, the entire solution architecture of Weather Station PoC and the its main components are shown:

![High Level Architecture](Images/PCH.WeatherStationHLA.png "High Level Architecture")

### Device
The main components on device side are:

#### Raspberry Pi 3 (model B)
The **Raspberry Pi 3** will be responsible for sending the temperature and humidity data retrieved from the sensor to the Azure IoT Hub service. The Integration between device and Azure IoT Hub will be done with a **IoT application** written in Python and with **Azure IoT Hub device SDK for Python** that will be described later.

For my PoC I reused an old Raspberry Pi 3 (model B) that I bought about a year ago. Raspberry Pi 3 Model B was released in February 2016 with a 1.2 GHz 64-bit quad core ARM Cortex-A53 processor, on-board 802.11n Wi-Fi, Bluetooth and USB boot capabilities.

I have also installed the **Raspberry Pi OS with desktop and recommended software** on Rasperry Pi 3. 

Raspberry Pi OS is a free operating system based on Debian optimized for the Raspberry Pi hardware. For further information this is the [link](https://www.raspberrypi.org/software/operating-systems/) where you can find the official documentation.

With Raspbian being the official OS for the Raspberry Pi,  I think that is a better choice for new users to start with due to it being so widely adopted and there are tons of examples and guidelines.


#### DHT-22 Sensor
The **DHT22 Sensor** will be responsible for retrieving the temperature and humidity data from environment. The Integration between Sensor and the Rasperry device will be done through a Python Library named **Adafruit_DHT Library**.  The details about Python Library will be discussed later.

The DHT22 (also named as AM2302) is a basic, low-cost digital temperature and humidity sensor. It uses a capacitive humidity sensor and a thermistor to measure the surrounding air, and spits out a digital signal on the data pin (no analog input pins needed). It uses a capacitive humidity sensor and a thermistor to measure the surrounding air. Temperature measuring range of the DHT22 is from -40°C to +125°C with ±0.5 degrees accuracy, and humidity measuring range, from 0% to 100% with accuracy from 2% to 5%.

![DHT22 Sensor](Images/DHT22.png "DHT22 Sensor")

I bought the sensor on the following site [link](https://az-delivery.de).

The DHT-22 sensor is attached to a Raspeberry Pi 3 as shown on connection diagram below:

![Raspberry](Images/Raspberry.png "Connecting the sensor with Rasperry Pi")


#### Final Result
In the followed picture my DHT-22 and Raspberry Pi:

![RaspberryAndDHT22](Images/RaspberryAndDHT22.png "Final result")


### Azure Components

#### Azure IoT HUB

As previously mentioned azure Iot hub is a managed service, hosted in the cloud, that acts as a central message hub for bi-directional communication between your IoT application and the devices it manages. 

Azure IoT Hub offers two tiers, **basic** and **standard**, that differ in the number of features they support. If your IoT solution is based around collecting data from devices and analyzing it centrally, then the basic tier is probably right for you. 

IoT Hub also offers a **free tier** that is meant for testing and evaluation. It has all the capabilities of the standard tier, but limited messaging allowances. You cannot upgrade from the free tier to either basic or standard. For my PoC I chose the free tier option.

To provision a new IoT Hub I used the following command:

```
az iot hub create --name %iot_hub_name% --resource-group %resource_group_name% --sku F1 --partition-count 2 --location %location%
```

For more information on which tier is right for your solution this is an useful [link](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-scaling?branch=release-iotbasic#basic-and-standard-tiers)


#### Azure Function

### Simulator
...

### Client Applications
...

# Develop the solution
...
## Device and Sensor test
I chose to use python for the development of the application on the device that will have to read the data from the sensor and send them to the Iot Hub. 

### Adafruit_DHT Library
As a starting point I used the **Adafruit_DHT** python library recommended by the sensor vendor. Before proceed, it we first need to make sure that Raspberry Pi OS is up to date and there are all software installed.

Before proceeding, we must first make sure that the Raspberry Pi operating system is up to date and that all software is installed. To do that, follow the steps below:

1. Start your Raspberry Pi, open terminal and run these commands:

2. First command is for making the system up to date:

```
sudo apt-get update && sudo apt-get upgrade -y
```

3. Second command is for installing python3-pip app. We will use pip app to install library:

```
sudo apt-get install python3-pip
```

4. Third command is for installing other apps that pip app uses during installation:

```
sudo python3 -m pip install --upgrade pip setuptools wheel
```

5. And after this is completed, we can install library that we need. Run thiscommand in terminal:

```
sudo pip3 install Adafruit_DHT
```

Before writing my first script to test the **Adafruit_DHT library**, I also installed my favorite editor [Visual Studio Code](https://code.visualstudio.com/).

6. Install Visual Studio Code

7. Open Visual Studio Code editor in Raspberry Pi OS, and copy-paste the following python script

```
import Adafruit_DHT
from time import sleep
sensor = Adafruit_DHT.DHT22

# DHT22 sensor connected to GPIO12.
pin = 12
print("[press ctrl+c to end the script]")
try: # Main program loop
   while True:
     humidity, temperature = Adafruit_DHT.read_retry(sensor, pin)

sleep(2.5)
if humidity is not None and temperature is not None:
    print("Temp={0:0.1f}*C Humidity={1:0.1f}%".format(temperature, humidity))
else:
    print("Failed to get reading. Try again!")

# Scavenging work after the end of the program
except KeyboardInterrupt:
    print("Script end!")
```

8. Save this script as **DHT22.py** and to run it, run this command in terminal:

```
python3 DHT22.py
```

And the output should look like this:

![TestScript](Images/TestScript.png "Output test script")

Now that we have tested our hardware, we can move on to analyze the architecture of the PoC




To be continued...
