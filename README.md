# Sunseeker Lawn Mower Control

This repository contains the MQTT commands for various whitelabeled Sunseeker lawn mower robots. The following brands are included:
- **Brucke RMxxx** (Tokmanni, Finland)
- **Scheppach** (Germany)
- **Orbex**
- **Meec tools** (Jula, Sweden)

Reverse engineering commands is still work in progress. Basic commands for controlling the robot or setting the cutting schedule are tested.

## Motivation

All of the commands were far too easy to reverse engineer from the lawn mower and thus this information is trivial to acquire for anyone willing to perform harmful acts. Robot publishes all the personal information fed to it over unsecured MQTT (yes, this includes your WIFI SSID + Password). By providing a list of known commands I can try to lessen your effort on localizing your robot or at least to give an overview of the commands someone could send to your robot.

If you do not have isolated WIFI network for IoT devices, I would highly recommend not to use WIFI at all.

## Localizing the robot
### 1. Custom NAT + MQTT broker
Some of the more advanced routers support custom NAT that could be used for redirecting the MQTT trafficking to your own MQTT broker. Note that the client (lawn mower) can use any port, but the server port is always 1883.

In Mikrotik RouterOS environment custom NAT can be created with following command:
```
/ip firewall nat add chain=dstnat src-address=192.168.0.52 protocol=tcp dst-port=1883 action=dst-nat to-addresses=192.168.0.109 to-ports=11883 comment="Redirect Sunseeker MQTT traffic" 
```
This command will redirect MQTT traffic from the Sunseeker lawn mower at IP address `192.168.0.52` to your MQTT broker at `192.168.0.109:11883`.


## MQTT Commands

Below are the MQTT commands used to control and query the status of these lawn mower robots.

### Basic Commands

- **Stop**
  ```
  /device/{deviceID}/get, Message: {"cmd":101,"mode":0}
  ```
- **Cut**
  ```
  /device/{deviceID}/get, Message: {"cmd":101,"mode":1}
  ```
- **Return Dock**
  ```
  /device/{deviceID}/get, Message: {"cmd":101,"mode":2}
  ```
- **Edge Cutting**
  ```
  /device/{deviceID}/get, Message: {"cmd":101,"mode":4}
  ```

### Query Commands

- **Update Robot Status**
  ```
  /device/{deviceID}/get, Message: {"cmd":200}
  ```
- **Query Robot Status**
  ```
  /device/{deviceID}/get, Message: {"cmd":201}
  ```
- **Query Robot Name**
  ```
  /device/{deviceID}/get, Message: {"cmd":202}
  ```
- **Query Cutting Schedule**
  ```
  /device/{deviceID}/get, Message: {"cmd":203}
  ```
- **Query Device Password**
  ```
  /device/{deviceID}/get, Message: {"cmd":204}
  ```
- **Query Rain Delay**
  ```
  /device/{deviceID}/get, Message: {"cmd":205}
  ```
- **Query Language**
  ```
  /device/{deviceID}/get, Message: {"cmd":206}
  ```
- **Query Starting Points**
  ```
  /device/{deviceID}/get, Message: {"cmd":207}
  ```
- **Query Robot Hardware Info**
  ```
  /device/{deviceID}/get, Message: {"cmd":208}
  ```
- **Query Ultrasonic**
  ```
  /device/{deviceID}/get, Message: {"cmd":209}
  ```
- **Query LEDs**
  ```
  /device/{deviceID}/get, Message: {"cmd":210}
  ```

### Set Commands

- **Set Clock**
  ```
  /device/{deviceID}/get, Message: {"cmd":102,"hms":223924,"week":6,"ymd":20240525}
  ```
- **Set Cutting Schedule**
  ```
  /device/{deviceID}/get, Message: {"Thu":{"slice":[{"end":1080,"start":600}],"trimming":true},"auto":false,"Tue":{"slice":[{"end":1080,"start":600}],"trimming":true},"Wed":{"slice":[{"end":1080,"start":600}],"trimming":true},"Sat":{"slice":[{"end":1080,"start":600}],"trimming":true},"cmd":103,"Fri":{"slice":[{"end":1080,"start":600}],"trimming":true},"Mon":{"slice":[{"end":1080,"start":600}],"trimming":true},"Sun":{"slice":[{"end":1080,"start":600}],"trimming":true},"pause":false}
  ```
- **Set Rain Delay**
  ```
  /device/{deviceID}/get, Message: {"cmd":105,"rain_delay_set":181,"rain_en":true}
  ```
- **Set Device Name**
  ```
  /device/{deviceID}/get, Message: {"cmd":107,"rename":"rotisko"}
  ```
- **Set Starting Points**
  ```
  /device/{deviceID}/get, Message: {"cmd":108,"mul_Mode":0,"mul_auto":false,"mul_en":true,"mul_meter1":0,"mul_meter2":0,"mul_meter3":0,"mul_meter4":0,"mul_pro1":25,"mul_pro2":25,"mul_pro3":25,"mul_pro4":25,"mul_zon1":43,"mul_zon2":34,"mul_zon3":0,"mul_zon4":95}
  ```

### Miscellaneous Commands

- **Restart Communications**
  ```
  /device/{deviceID}/get, Message: {"cmd":109}
  ```
- **Reset Device Password**
  ```
  /device/{deviceID}/get, Message: {"cmd":112}
  ```
- **Restart Data Manager Service**
  ```
  /device/{deviceID}/get, Message: {"cmd":113}
  ```
- **Set Ultrasonic**
  ```
  /device/{deviceID}/get, Message: {"cmd":114}
  ```
- **Set LEDs**
  ```
  /device/{deviceID}/get, Message: {"cmd":114}
  ```
- **Restart Robot**
  ```
  /device/{deviceID}/get, Message: {"cmd":119}
  ```
- **OTA?**
  ```
  /device/{deviceID}/get, Message: {"cmd":119}
  ```
- **Unknown**
  ```
  /device/{deviceID}/get, Message: {"result":1,"cmd":300,"command":522}
  ```
