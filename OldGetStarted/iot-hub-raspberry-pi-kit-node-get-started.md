---
platform: raspbian
services: iot-hub, stream-analytics, event-hubs, web-apps, documentdb, storage-accounts
language: node
---

# Connect Raspberry Pi to Azure IoT Hub in the cloud

In this tutorial, you begin by learning the basics of working with Raspberry Pi that's running Raspbian. You then learn how to seamlessly connect your devices to the cloud by using [Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-what-is-iot-hub). For Windows 10 IoT Core samples, go to the [Windows Dev Center](http://www.windowsondevices.com/).

Don't have a kit yet? Buy a new kit [here](https://azure.microsoft.com/develop/iot/starter-kits).


## What you do

* Setup Raspberry Pi.
* Create an IoT hub.
* Register a device for Pi in your IoT hub.
* Run a sample application on Pi to send sensor data to your IoT hub.

Connect Raspberry Pi to an IoT hub that you create. Then you run a sample application on Pi to collect temperature and humidity data from a BME280 sensor. Finally, you send the sensor data to your IoT hub.

## What you learn

* How to create an Azure IoT hub and get your new device connection string.
* How to connect Pi with a BME280 sensor.
* How to collect sensor data by running a sample application on Pi.
* How to send sensor data to your IoT hub.

## What you need

![What you need](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* The Raspberry Pi 2 or Raspberry Pi 3 board.
* An active Azure subscription. If you don't have an Azure account, [create a free Azure trial account](https://azure.microsoft.com/free/) in just a few minutes.
* A monitor, a USB keyboard, and mouse that connect to Pi.
* A Mac or a PC that is running Windows or Linux.
* An Internet connection.
* A 16 GB or above microSD card.
* A USB-SD adapter or microSD card to burn the operating system image onto the microSD card.
* A 5-volt 2-amp power supply with the 6-foot micro USB cable.

The following items are optional:

* An assembled Adafruit BME280 temperature, pressure, and humidity sensor.
* A breadboard.
* 6 F/M jumper wires.
* A diffused 10-mm LED.


> **NOTE:** 
These items are optional because the code sample support simulated sensor data.

## Create an IoT hub

1. In the [Azure portal](https://portal.azure.com/), click **New** > **Internet of Things** > **IoT Hub**.

   ![Create an iot hub in the Azure portal](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
1. In the **IoT hub** pane, enter the following information for your IoT hub:

   **Name**: It is the name for your IoT hub. If the name you enter is valid, a green check mark appears.

   **Pricing and scale tier**: Select the free F1 tier. This option is sufficient for this demo. See [pricing and scale tier](https://azure.microsoft.com/pricing/details/iot-hub/).

   **Resource group**: Create a resource group to host the IoT hub or use an existing one. See [Using resource groups to manage your Azure resources](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal).

   **Location**: Select the closest location to you where the IoT hub is created.

   **Pin the dashboard**: Check this option for easy access to your IoT hub from the dashboard.

   ![Fill in the fields for creating your Azure IoT hub](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

1. Click **Create**. It could take a few minutes for your IoT hub to be created. You can see progress in the **Notifications** pane.

   ![See notifications of your IoT hub creation progress](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

1. Once your IoT hub is created, click it from the dashboard. Make a note of the **Hostname**, and then click **Shared access policies**.

   ![Get the hostname of your IoT hub](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. In the **Shared access policies** pane, click the **iothubowner** policy, and then copy and make a note of the **Connection string** of your IoT hub. For more information, see [Control access to IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-security).

   ![Get your IoT hub connection string](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## Register a device in the IoT hub for the your device

1. In the [Azure portal](https://portal.azure.com/), open your IoT hub.
1. Click **Device Explorer**.
1. In the Device Explorer pane, click **Add** to add a device to your IoT hub.

   **Device ID**: The ID of the new device.

   **Authentication Type**: Select **Symmetric Key**.

   **Auto Generate Keys**: Check this field.

   **Connect device to IoT Hub**: Click **Enable**.

   ![Add a device in the device explorer of your IoT hub](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

1. Click **Save**.
1. After the device is created, open the device in the **Device Explorer** pane.
1. Make a note of the primary key of the connection string.

   ![Get the device connection string](https://github.com/Microsoft/azure-docs/raw/master/articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)


## Setup Raspberry Pi

### Install the Raspbian operating system for Pi

Prepare the microSD card for installation of the Raspbian image.

1. Download Raspbian.
   -  [Download Raspbian Jessie with Pixel](https://www.raspberrypi.org/downloads/raspbian/) (the .zip file).
   -  Extract the Raspbian image to a folder on your computer.
2. Install Raspbian to the microSD card.
   -  [Download and install the Etcher SD card burner utility](https://etcher.io/).
   -  Run Etcher and select the Raspbian image that you extracted in step 1.
   -  Select the microSD card drive. Note that Etcher may have already selected the correct drive.
   -  Click Flash to install Raspbian to the microSD card.
   -  Remove the microSD card from your computer when installation is complete. It's safe to remove the microSD card directly because Etcher automatically ejects or unmounts the microSD card upon completion.
   -  Insert the microSD card into Pi.

### Enable SSH and I2C

1. Connect Pi to the monitor, keyboard and mouse, start Pi and then log in Raspbian by using `pi` as the user name and `raspberry` as the password.
2. Click the Raspberry icon > **Preferences** > **Raspberry Pi Configuration**.

   ![The Raspbian Preferences menu](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

3. On the **Interfaces** tab, set **I2C** and **SSH** to **Enable**, and then click **OK**.

   ![Enable I2C and SSH on Raspberry Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> **NOTE:** 
To enable SSH and I2C, you can find more reference documents on [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) and [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2).

### Connect the sensor to Pi

Use the breadboard and jumper wires to connect an LED and a BME280 to Pi as follows. If you don’t have the sensor, skip this section.

![The Raspberry Pi and sensor connection](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)


For sensor pins, use the following wiring:

| Start (Sensor & LED)     | End (Board)            | Cable Color   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3.3V PWR (Pin 1)       | White cable   |
| GND (Pin 7G)             | GND (Pin 6)            | Brown cable   |
| SCK (Pin 8G)             | I2C1 SDA (Pin 3)       | Orange cable  |
| SDI (Pin 10G)            | I2C1 SCL (Pin 5)       | Red cable     |
| LED VDD (Pin 18F)        | GPIO 24 (Pin 18)       | White cable   |
| LED GND (Pin 17F)        | GND (Pin 20)           | Black cable   |

Click to view [Raspberry Pi 2 & 3 Pin mappings](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) for your reference.

After you've successfully connected BME280 to your Raspberry Pi, it should be like below image.

![Connected Pi and BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### Connect Pi to the network

Turn on Pi by using the micro USB cable and the power supply. Use the Ethernet cable to connect Pi to your wired network or follow the [instructions from the Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) to connect Pi to your wireless network. After your Pi has been successfully connected to the network, you need to take a note of the [IP address of your Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Connected to wired network](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)


## Run a sample application on Pi

### Clone sample application and install the prerequisite packages

1. Use one of the following SSH clients from your host computer to connect to your Raspberry Pi.
    - [PuTTY](http://www.putty.org/) for Windows.
    - The built-in SSH client on Ubuntu or macOS.

2. Clone the sample application by running the following command:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

3. Install all packages by the following command. It includes Azure IoT device SDK, BME280 Sensor library and Wiring Pi library.

   ```bash
   cd iot-hub-node-raspberry-pi-clientapp
   npm install
   ```
   > **NOTE:** 
   It might take several minutes to finish this installation process denpening on your network connection.

### Configure the sample application

1. Open the config file by running the following commands:

   ```bash
   nano config.json
   ```

   ![Config file](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   There are two items in this file you can configurate. The first one is `interval`, which defines the time interval between two messages that send to cloud. The second one `simulatedData`,which is a Boolean value for whether to use simulated sensor data or not.

   If you **don't have the sensor**, set the `simulatedData` value to `true` to make the sample application create and use simulated sensor data.

2. Save and exit by pressing Control-O > Enter > Control-X.

### Run the sample application

1. Run the sample application by running the following command:

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > **NOTE:** 
   Make sure you copy-paste the device connection string into the single quotes.


You should see the following output that shows the sensor data and the messages that are sent to your IoT hub.

![Output - sensor data sent from Raspberry Pi to your IoT hub](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## Next steps

You’ve run a sample application to collect sensor data and send it to your IoT hub.

You have now learned how to run a sample application that collects sensor data and sends it to your IoT hub. To explore how to store, analyze and visualize the data from this application in Azure using a variety of different services, please click on the following lessons:

-   [Manage cloud device messaging with iothub-explorer]
-   [Save IoT Hub messages to Azure data storage]
-   [Use Power BI to visualize real-time sensor data from Azure IoT Hub]
-   [Use Azure Web Apps to visualize real-time sensor data from Azure IoT Hub]
-   [Weather forecast using the sensor data from your IoT hub in Azure Machine Learning]
-   [Remote monitoring and notifications with Logic Apps]   

[Manage cloud device messaging with iothub-explorer]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-explorer-cloud-device-messaging
[Save IoT Hub messages to Azure data storage]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-store-data-in-azure-table-storage
[Use Power BI to visualize real-time sensor data from Azure IoT Hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi
[Use Azure Web Apps to visualize real-time sensor data from Azure IoT Hub]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-live-data-visualization-in-web-apps
[Weather forecast using the sensor data from your IoT hub in Azure Machine Learning]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-weather-forecast-machine-learning
[Remote monitoring and notifications with Logic Apps]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps
