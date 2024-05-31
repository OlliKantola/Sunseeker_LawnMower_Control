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

Below are the MQTT commands used to control and query the status of these lawn mower robots. Message payload is in JSON format.

Command Topic:\
**/device/{deviceID}/get**

Response Topic:\
**/device/{deviceID}/update**

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
  {"cmd":201}

  {
    "cmd": 501,
    "mode": 0,
    "power": 100,
    "station": true,
    "on_area": 4005,
    "on_min": 6224,
    "total_min": 77501,
    "cur_min": 0,
    "cur_area": 0,
    "wifi_lv": 3
  }
  ```
  
- **Query Robot Name**
  ```
  {"cmd":202}

  {"cmd":502,"name":"MyMower"}
  ```
- **Query Cutting Schedule**
  ```
  {"cmd":203}

  {
    "cmd": 503,
    "auto": false,
    "Sun": {
      "slice": [
        {
          "start": 540,
          "end": 900
        }
      ],
      "Trimming": true
    },
    "Mon": {},
    "Tue": {},
    "Wed": {},
    "Thu": {},
    "Fri": {},
    "Sat": {}
  }
  ```
- **Query Device Password**
  ```
  {"cmd":204}

  {"cmd":504,"passwd":0}
  ```
- **Query Rain Delay**
  ```
  {"cmd":205}

  {
    "cmd": 505,
    "rain_en": true,
    "rain_status": 0,
    "rain_delay_set": 180,
    "rain_delay_left": 180
  }
  ```
- **Query Language**
  ```
  {"cmd":206}

  {"cmd":506,"language":0}
  ```
- **Query Starting Points**
  ```
  {"cmd":207}

  {
    "cmd": 507,
    "mul_en": true,
    "mul_auto": false,
    "mul_zon1": 0,
    "mul_zon2": 25,
    "mul_zon3": 50,
    "mul_zon4": 75
  }
  ```
- **Query Robot Hardware Info**
  ```
  {"cmd":208}

  {
    "cmd": 508,
    "sn": "XXXXXXXXXXXXXXXXXXXX",
    "model": "RMA501M20V",
    "avail": "market_active",
    "version": 23202,
    "name": "MyMower",
    "mb": {
      "hv": 22100,
      "sv": 22905
    },
    "bb": {
      "hv": 210700,
      "sv": 40901
    },
    "db": {
      "hv": 60300,
      "sv": 61107
    },
    "lb": {
      "hv": 0,
      "sv": 0
    },
    "btl": {
      "sv": 40501
    }
  }
  ```
- **Query Ultrasonic**
  My robot doesn't have ultrasonic sensor.
  ```
  {"cmd":209}

  {
    "cmd": 515,
    "ultra_en": 0,
    "ultra_lv": 0
  }
  ```
- **Query LEDs**
  ```
  My robot doesn't have LED "driving" lights.
  {"cmd":210}

  {
    "cmd": 516,
    "led_en": 0,
    "led_mode": 0,
    "led_start": 0,
    "led_end": 0,
    "led_night": 0
  }
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
  {"cmd":105,"rain_delay_set":181,"rain_en":true}

  {"cmd":400,"command":105,"result":true}
  {"cmd":509,"lv":3,"log":"I/rain [Fri May 31 21:31:13 2024] (259)set rain config success, enable=1, rain delay =181minutes\n"}
  {"cmd":509,"lv":3,"log":"I/userset [Fri May 31 21:31:13 2024] (893)set rain enable = 1, delay=181 success\n"}
  {"cmd":505,"rain_en":true,"rain_status":0,"rain_delay_set":181,"rain_delay_left":181}
  ```
- **Set Device Name**
  ```
  {"cmd":107,"rename":"rotisko"}

  {"cmd":400,"command":107,"result":true}
  {"cmd":509,"lv":3,"log":"I/userset [Fri May 31 21:23:26 2024] (980)set name=rotisko success\n"}
  ```
- **Set Starting Points**
  ```
  {"cmd":108,"mul_Mode":0,"mul_auto":false,"mul_en":true,"mul_meter1":0,"mul_meter2":0,"mul_meter3":0,"mul_meter4":0,"mul_pro1":25,"mul_pro2":25,"mul_pro3":25,"mul_pro4":25,"mul_zon1":43,"mul_zon2":34,"mul_zon3":0,"mul_zon4":95}

  {"cmd":400,"command":108,"result":true}
  {"cmd":507,"mul_en":true,"mul_auto":false,"mul_zon1":0,"mul_zon2":25,"mul_zon3":50,"mul_zon4":75}
  {"cmd":509,"lv":3,"log":"I/multizone [Fri May 31 21:25:45 2024] (470)update multizone border length=0mm, enable zone and disable auto, point1=1000, 2=1000, 3=1000, 4=1000\n"}
  {"cmd":509,"lv":3,"log":"I/multizone [Fri May 31 21:25:45 2024] (211)set multizone success, multizone enable=1, auto enable=0\n"}
  {"cmd":509,"lv":3,"log":"I/userset [Fri May 31 21:25:45 2024] (938)set multizone success, enable=1, auto=0, scale0=0, 1=25,2=50, 3=75\n"}
  ```

### Miscellaneous Commands

- **Restart Communications**
  ```
  /device/{deviceID}/get, Message: {"cmd":109}
  ```
- **Reset Device Password**
  ```
  {"cmd":112}

  {"cmd":400,"command":112,"result":true}
  {"cmd":509,"lv":3,"log":"I/userset [Fri May 31 21:46:55 2024] (1210)reset pwd success\n"}
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
