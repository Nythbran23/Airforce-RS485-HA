# airforce485-HA
Home Assistant yaml code for Future Energy Wind Turbine integration via RS485

This provides a working yaml code to copy into your configuration.yaml in Home Assistant.
This code is based on the use of a RS485 to Network adaptor (elc rsether used for testing) set up as a TCP server.
Connection to the Future Energy Wind Turbine Controller is via a twisted pair to the RS485 terminals.
The Adaptor Serial Port was set up with the following parameters:

![MODBUS TCP SETTINGS](https://user-images.githubusercontent.com/122684847/213724273-dcb0594c-0bda-4f3c-82aa-1280b36f9bdd.jpg)

You need to choose a suitable IP address for the adaptor to suit your network (in my case 192.168.2.90) and input it into the Local IPConfig menu.

Once connected I recommend you download the Radzio! Modbus Simulator to a PC on the same network to test.
Entering the settings as shown below (adjusting the IP address to the one you chose above) and with the Controller powered up you should see data streaming OK from the holding registers:

![MODBUS RADZIO TCP SETTINGS](https://user-images.githubusercontent.com/122684847/213724993-385df463-8522-4b97-96ad-e0d7dbc8d8f9.jpg)

Once the data flow is proved then you can add the .yaml code below to your configuration.yaml in Home Assistant. Adjust the IP address to be the same as you have set up and used above and reboot.

The holding registers are designated thus by the Contoller:

![WT-Holding Registers](https://user-images.githubusercontent.com/122684847/213725473-eaf67e09-65ac-4d57-9842-725c3712e844.jpg)

The initial commit code below will read:

Battery Voltage (the voltage of the battery the controller is connected to - in my case nominal 48V)
Turbine Current A
Turbine Power W
Turbine Speed RPM
Average Windspeed MPS
Wind Direction Deg

It is hoped to extend this to add additional indicators for alarms, power generation totals etc.

A typical output display in Home Assistant may be:

<img width="423" alt="WT_HA_EXAMPLE" src="https://user-images.githubusercontent.com/122684847/213727322-52417121-917a-4bc6-b200-2b0eb6a133cd.png">

```
#modbus details for wind turbine
modbus:
    name: "hub1"
    # close_comm_on_error: true
    delay: 2
    retries: 20
    type: rtuovertcp
    host: 192.168.2.90
    port: 502
    message_wait_milliseconds: 500
    sensors:
      - name: BATTERY_VOLTAGE
        unique_id: CB8151F76E0_0 # this, and all the entries below for each address can be anything unique - adjust as you want
        scan_interval: 10
        address: 0
        slave: 1
        scale: 0.1
        precision: 1
        input_type: holding
        unit_of_measurement: V
        device_class: voltage
        state_class: measurement
        data_type: uint16
      - name: TURBINE_CURRENT
        unique_id: CB8151F76E0_1
        scan_interval: 1
        address: 1
        slave: 1
        input_type: holding
        unit_of_measurement: A
        scale: 0.1
        precision: 1
        device_class: current
        state_class: measurement
        data_type: uint16
      - name: TURBINE_POWER
        unique_id: CB8151F76E0_2
        scan_interval: 1
        address: 2
        slave: 1
        input_type: holding
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        data_type: uint16
      - name: TURBINE_SPEED
        unique_id: CB8151F76E0_3
        scan_interval: 1
        address: 3
        slave: 1
        input_type: holding
        unit_of_measurement: RPM
        device_class: speed
        state_class: measurement
        data_type: uint16
      - name: WINDSPEED
        unique_id: CB8151F76E0_4
        scan_interval: 1
        address: 4
        slave: 1
        input_type: holding
        unit_of_measurement: MPS
        scale: 0.1
        precision: 1
        device_class: wind_speed
        state_class: measurement
        data_type: uint16
      - name: WIND_DIRECTION
        unique_id: CB8151F76E0_6
        scan_interval: 1
        address: 6
        slave: 1
        input_type: holding
        unit_of_measurement: Degrees
        device_class: distance
        state_class: measurement
        data_type: uint16
       ```
       
