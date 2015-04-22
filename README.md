# Purpose
Personal repository for creating IPSO-based objects. 

## IPSO Objects
Firstly introduced by LWM2M, and available on [Wakamaa](https://github.com/eclipse/wakaama) and Leshan implementaitons. As the OMA Lightweight M2M standard supports multiple instances of an Object, the structure of the IPSO Objects is different than in the original function set design. For example, if a device has multiple sensors it would register multiple instances of the IPSO Sensor Object.

It's main purpose is to...
*  ... ensure application interoperability.
*  ... make it simple to add new resources.
*  ... find common design pattern that is independent of the protocol used (CoAP, HTTP, MQTT, SNMP, TR69,...).
*  ... to provide the building blocks to semantically define more complex devices.

