# IoT Engineering


## 1. MQTT

**Charateristic of MQTT**
- Simple implement
- Quality data
- Light weight and bandwitdh
- Data agnostic: sensor data coming a variety.
- Continuous session aware: need to know when device is alive

**Data transfer using pub-sub model**

![](img/iot-1.png) 

**MQTT versioning:**
- v3.1.1 using TCP/IP
- stable v.5.0
- MQTT for sensor network: MQTT/SN using UDP instead of TCP.

**MQTT principles**:
- MQTT model follow pub-sub architecture instead of client server architect.
- Why we use pub sub model for IoT application, because client server architect cause complexity of for IoT application.
- Difference with **AMQP**:
  - Light weight: < 1MB suitable for sensor device
  - No websocket support
  
**MQTT Spec**:
- MQTT methods:
  - Conenct
  - Disconnect
  - Publish
  - Subcribe
  - Unsubcribe

- **MQTT features:**
  - **Wildcard**: allow consumer to subcribe to multiple topic by patterns
  - **Delivery sematic**:
    - At least one
    - At most one
    - Exact one
  - **Persistence session**: consumer subcribe and request MQTT broker keep the session alive, and save all info relate to topic, ... The clientId that the client provides when it establishes connection to the broker identifies the session. 
    - What is save in persitence session:
      - Client info about subcription
      - Message that not ack yet, these msg is deleted when client comeback online and consume it. 
  - **Retain message**: broker store few message per topic so that, when subcriber subcribe they receive the latest message. In other words, a retained message on a topic is the last known good value.
  - **Last Will and Testament**: when publisher shutdown ungracefully, broker send a default message to all consumers.
  - **Keep alive and client take-over**
  
- **Why use MQTT:**
  - HTTP: Client server pattern do not suitable for large amount of device and it does not have sematic delivery.
  - AMQP: not suitable for constraint device
  
- **Topic filtering:**
  - Wildcard subcribing:
    - "/" for sub level
    - "$" for system topic
    - "+" for get all in single level
    - "#" for get all in lower level