---
platform: linux
services: iot-hub, stream-analytics, event-hubs, web-apps, documentdb, storage-accounts
language: c
---

# Connect Intel Edison to Azure IoT Hub (C)

In this tutorial, you begin by learning the basics of working with Intel Edison. You then learn how to seamlessly connect your devices to the cloud by using [Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-what-is-iot-hub).

Don't have a kit yet? Start [here](https://azure.microsoft.com/develop/iot/starter-kits)

## What you do

* Setup Intel Edison and and Grove modules.
* Create an IoT hub.
* Register a device for Edison in your IoT hub.
* Run a sample application on Edison to send sensor data to your IoT hub.

Connect Intel Edison to an IoT hub that you create. Then you run a sample application on Edison to collect temperature and humidity data from a Grove temperature sensor. Finally, you send the sensor data to your IoT hub.

## What you learn

* How to create an Azure IoT hub and get your new device connection string.
* How to connect Edison with a Grove temperature sensor.
* How to collect sensor data by running a sample application on Edison.
* How to send sensor data to your IoT hub.

## What you need

![What you need](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* The Intel Edison board
* Arduino expansion board
* An active Azure subscription. If you don't have an Azure account, [create a free Azure trial account](https://azure.microsoft.com/free/) in just a few minutes.
* A Mac or a PC that is running Windows or Linux.
* An Internet connection.
* A Micro B to Type A USB cable
* A direct current (DC) power supply. Your power supply should be rated as follows:
  - 7-15V DC
  - At least 1500mA
  - The center/inner pin should be the positive pole of the power supply

The following items are optional:

* Grove Base Shield V2
* Grove - Temperature Sensor
* Grove Cable
* Any spacer bars or screws included in the packaging, including two screws to fasten the module to the expansion board and four sets of screws and plastic spacers.

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


## Setup Intel Edison

### Assemble your board

This section contains steps to attach your Intel® Edison module to your expansion board.

1. Place the Intel® Edison module within the white outline on your expansion board, lining up the holes on the module with the screws on the expansion board.

2. Press down on the module just below the words `What will you make?` until you feel a snap.

   ![assemble board 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Use the two hex nuts (included in the package) to secure the module to the expansion board.

   ![assemble board 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Insert a screw in one of the four corner holes on the expansion board. Twist and tighten one of the white plastic spacers onto the screw.

   ![assemble board 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Repeat for the other three corner spacers.

   ![assemble board 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Now your board is assembled.

   ![assembled board](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### Connect the Grove Base Shield and the temperature sensor

1. Place the Grove Base Shield on to your board. Make sure all pins are tightly plugged into your board.
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Use Grove Cable to connect Grove temperature sensor onto the Grove Base Shield **A0** port.

   ![Connect to temperature sensor](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Edison and sensor connection](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Now your sensor is ready.

### Power up Edison

1. Plug in the power supply.

   ![Plug in power supply](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. A green LED(labeled DS1 on the Arduino* expansion board) should light up and stay lit.

3. Wait one minute for the board to finish booting up.

   > **NOTE:**
   > If you do not have a DC power supply, you can still power the board through a USB port. See `Connect Edison to your computer` section for details. Powering your board in this fashion may result in unpredictable behavior from your board, especially when using Wi-Fi or driving motors.

### Connect Edison to your computer

1. Toggle down the microswitch towards the two micro USB ports, so that Edison is in device mode. For differences between device mode and host mode, please reference [here](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Toggle down the microswitch](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Plug the micro USB cable into the top micro USB port.

   ![Top micro USB port](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Plug the other end of USB cable into your computer.

   ![Computer USB](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. You will know that your board is fully initialized when your computer mounts a new drive (much like inserting a SD card into your computer).

## Download and run the configuration tool
Get the latest configuration tool from [this link](https://software.intel.com/en-us/iot/hardware/edison/downloads) listed under the `Installers` heading. Execute the tool and follow its on-screen instructions, clicking Next where needed

### Flash firmware
1. On the `Set up options` page, click `Flash Firmware`.
2. Select the image to flash onto your board by doing one of the following:
   - To download and flash your board with the latest firmware image available from Intel, select `Download the latest image version xxxx`.
   - To flash your board with an image you already have saved on your computer, select `Select the local image`. Browse to and select the image you want to flash to your board.
3. The setup tool will attempt to flash your board. The entire flashing process may take up to 10 minutes.

### Set password
1. On the `Set up options` page, click `Enable Security`.
2. You can set a custom name for your Intel® Edison board. This is optional.
3. Type a password for your board, then click `Set password`.
4. Mark down the password, which is used later.

### Connect Wi-Fi
1. On the `Set up options` page, click `Connect Wi-Fi`. Wait up to one minute as your computer scans for available Wi-Fi networks.
2. From the `Detected Networks` drop-down list, select your network.
3. From the `Security` drop-down list, select the network's security type.
4. Provide your login and password information, then click `Configure Wi-Fi`.
5. Mark down the IP address, which is used later.

> **NOTE:**
> Make sure that Edison is connected to the same network as your computer. Your computer connects to your Edison by using the IP address.

   ![Connect to temperature sensor](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Congratulations! You've successfully configured Edison.

## Run a sample application on Intel Edison

### Prepare the Azure IoT Device SDK

1. Use one of the following SSH clients from your host computer to connect to your Intel Edison. The IP address is from the configuration tool and the password is the one you've set in that tool.
    - [PuTTY](http://www.putty.org/) for Windows.
    - The built-in SSH client on Ubuntu or macOS.

2. Clone the sample client app to your device. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Then navigate to the repo folder to run the following command to build Azure IoT SDK

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### Configure the sample application

1. Open the config file by running the following commands:

   ```bash
   nano config.h
   ```

   ![Config file](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   There are two macros in this file you can configurate. The first one is `INTERVAL`, which defines the time interval between two messages that send to cloud. The second one `SIMULATED_DATA`,which is a Boolean value for whether to use simulated sensor data or not.

   If you **don't have the sensor**, set the `SIMULATED_DATA` value to `1` to make the sample application create and use simulated sensor data.

2. Save and exit by pressing Control-O > Enter > Control-X.

### Build and run the sample application

1. Build the sample application by running the following command:

   ```bash
   cmake . && make
   ```
   ![Build output](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

2. Run the sample application by running the following command:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > **NOTE:**
   Make sure you copy-paste the device connection string into the single quotes.

You should see the following output that shows the sensor data and the messages that are sent to your IoT hub.

![Output - sensor data sent from Intel Edison to your IoT hub](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

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
