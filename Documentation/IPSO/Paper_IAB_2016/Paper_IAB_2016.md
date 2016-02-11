<p align="right">
	<img src="http://wwwold.ipso-alliance.org/wp-content/media/ipso-CMYK-dark-01.png" width="150">
</p>

# IP for Smart Objects - Smart Objects 
## IoT Semantic Interoperability Workshop 2016 
IPSO Smart Object Committee - 8.2.2016

> _**Disclaimer**_
> 
>**This is a work in progress**, compiled from documents from Michael, Jaime and B. Králik guidelines, the OMALWM2M spec, CoRE documents and other sources. Please revise, edit, etc.


# Table of Contents
1. [Introduction](#Introduction)
2. [Components](#Data Model Components)
3. [Linking of Objects](#Linking of Objects)
4. [Example](#Example and Extended References)
4.3. [Starter Pack](#Smart Objects - Starter Pack)
4.4. [Expansion Pack](#Smart Objects - Expansion Pack)
4.5. [Application Specific Objects](#Smart Objects - Application Specific Objects)
5. [References](#References)

## 1. Introduction

Standards for constrained devices are rapidly consolidating. The availability of Internet Protocol (IP) on constrained devices has enabled at the network level towards the Internet. The IETF has also specified a set of standard protocols for such IP-enabled devices to work in a Web-like fashion. One of such protocols is the Constrained Application Protocol (RFC 7252) [1] that provides similar methods, URIs, Discovery mechanisms, etc. as HTTP but for the constrained environment.

On top of such Application Protocols there is a need for structuring of the data shared between the devices, mainly due to originally CoAP not providing a proper resource structure (NOTE: SENML!).   

IPSO Smart Objects provide a common design pattern, an object model, to provide high level interoperability between Smart Object devices and connected software applications on other devices and services.  IPSO Objects are agnostic of the application protocol and thus do not mandate CoAP-like protocols by default although they are widely used. 

The Object model is based on the Open Mobile Alliance Lightweight Specification (OMA LWM2M) [2], which is a set of management interfaces built on top of CoAP in order to enable device management operations (bootstrapping, firmware updates, error reporting, etc). While LWM2M uses objects with fixed mandatory resources, IPSO Smart Objects use a more reusable design. 


As already mentioned, the object model can optionally be used with any protocol, for example http, that supports the web standard content types and REST methods defined in the HTTP specification (RFC 2616)[3]. Other usage examples are with more publish/subscribe type of protocols such as MQTT or DDS, thus encapsulating the objects and publishing them under some topic. 

## 2. Data Model Components

The data model for IPSO Smart Objects consists of 5 parts:

1. Class Hierarchy
2. Schema
3. Data types 
4. Operations 
5. Content formats

### 2.1. Class hierarchy 

The URI template defines an implicit class hierarchy consisting of Objects and Resources, The Object class is further split into ID and Instance subclasses. 

Objects are typed containers which define the semantic type of instances. Instances inherit the type of their parent object, and allow Smart Object endpoints to expose multiple sensors and actuators of a particular type. Object instances are themselves containers for resources, which are the observable properties of an object. 

This simple class hierarchy enables application software to consume device APIs using a simple, well known state machine and mapping structure. For complex objects, we allow linking of an object to another object through an object link resource. This enables the recursion to be handled at the object level, using design patterns similar to web linking. An application client can consume a device’s API without knowing it’s structure and attributes a priori.

IPSO Smart Objects define behavior at the Object and instance scope, for example the tracking of minimum and maximum values, reset of accumulated values

### 2.2. Schema


#### 2.2.1. URI Template

The URI Template defined in LWM2M is used for IPSO Smart Objects consists of three unsigned 16-bit integers separated by the character `/`:

```
<object ID>/<object instance ID>/<resource ID>
```
Semantically, the object type represents a single measurement, actuation, or control point, for example a temperature sensor, a light (actuator), or an on-off switch (control point).

The semantic meaning of a resource specifies a particular view or active property of an object. For example, a temperature sensor object might expose the current value (most recent reading), also the minimum and maximum possible reading, the minimum and maximum reading in an interval, and attributes like engineering units and application type.

For example, a temperature sensor example URI would be `3303/0/5700`:

```
3300  -> Temperature Sensor
0     -> instance 0 of a Temperature Sensor
5700  -> resource having the current value or a most recent reading
```
#### 2.2.2 Attributes
Attributes describe the metadata configuration, settings, and state of an object or resource, and are discoverable by reading the link-format data of an object or resource. Multiple attributes may be serialized in the link-format descriptors that a Smart Object exposes. 

Some attributes are immutable for a given object or resource type, for example the static read, write, and execute capability attributes are derived from a Smart Object’s definition file, while other like the LWM2M Notification Attributes are used to dynamically configure a particular object instance or resource.

Attributes are represented using IETF CoRE Link-Format (RFC 6690) or equivalent mapping to other content formats e.g. application/json+ld


### 2.3. Data types 

There are 7 data types, same as the ones defined in OMA LWM2M [2].

1. String: A UTF-8 string, the minimum and/or maximum length of the String MAY be defined.
2. Integer: An 8, 16, 32 or 64-bit signed integer. The valid range of the value for a Resource SHOULD be defined. This data type is also used for the purpose of enumeration. 
3. Float: A 32 or 64-bit floating point value. The valid range of the value for a Resource SHOULD be defined.
4. Boolean: An integer with the value 0 for False and the value 1 for True.
5. Opaque: A sequence of binary octets, the minimum and/or maximum length of the String MAY be defined.
6. Time: Unix Time. A signed integer representing the number of seconds since Jan 1st, 1970 in the UTC time zone.
7. Object Link: The object link is used to refer an Instance of a given Object. An Object link value is composed of two concatenated 16-bits unsigned integers following the Network Byte Order convention.


### 2.4. Operations 
IPSO Objects and their resources have the same operations as their counterparts in [2] with the same semantics.
*	Resource values: Read, Write, Execute (restricted by the Access Type field)
*	Object Instances: Create, Delete (restricted by the Multiple Instances field)
*	Objects and their instances: Read, Write
*	Attributes: Set, Discover


### 2.5. Content formats

Content formats are those specified by the LWM2M Technical Specification [2]:
*	Resource values: text/plain, tlv
*	Objects: text/senml+json, application/cbor, binary/tlv
*	Attributes: link-format, link-format+json

## 3. Linking of Objects

To create composite Objects, the object may be of a single reusable type “generic composite object” with one ID, and the resources may be of a generic reusable link type, also using a single ID, with multiple instances allowed, e.g. 4000/0/6700/0 where 4000 is “composite object” and 6700 is “generic object link”. 

> _**NOTE**_
> This part is very weak, please edit.


# ![IPSO Object](https://github.com/IPSO-Alliance/SmartObjectGuidelines/blob/master/Paper_IAB_2016/linking.png)

## 4. Examples and Extended References

### 4.1 Definition documents

For practical purposes we require a format for representing the objects in an unambiguous fashion. IPSO has used XML to the XML Schema in Appendix A, either hand-written or automatically generated from other sources. In the XML version, Integers, Floats and Time values must be represented as in the above subsection. Binary (Opaque) values must be represented in Base64-encoded form. Booleans must be represented using the lower-case strings true and false. XML definition documents may reference other XML definition documents using Include [5].

### 4.2 Humidity Sensor 

The following is a example of a humidity sensor that contains the sensor value, units, min and max measured values, min and max range values and a reste of those. 
# ![IPSO Object](https://github.com/IPSO-Alliance/SmartObjectGuidelines/blob/master/Paper_IAB_2016/humidity_object.png)


The following is the definition document for the Humidity Object in XML.


```
<?xml version="1.0" encoding="utf-8"?>
<LWM2M>
	<Object ObjectType="MODefinition">
		<Name>Humidity</Name>
		<Description1>Description: This IPSO object should be used with a humidity sensor to report a humidity measurement.  It also provides resources for minimum/maximum measured values and the minimum/maximum range that can be measured by the humidity sensor. An example measurement unit is relative humidity as a percentage (ucum:%).</Description1>
		<ObjectID>3304</ObjectID>
		<ObjectURN>urn:oma:lwm2m:ext:3304</ObjectURN>
		<MultipleInstances>Multiple</MultipleInstances>
		<Mandatory>Optional</Mandatory>
		<Resources>
			<Item ID="5700">
				<Name>Sensor Value</Name>
				<Operations>R</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Mandatory</Mandatory>
				<Type>Float</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units>Defined by “Units” resource.</Units>
				<Description>Last or Current Measured Value from the Sensor</Description>
			</Item>
			<Item ID="5601">
				<Name>Min Measured Value</Name>
				<Operations>R</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Optional</Mandatory>
				<Type>Float</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units>Defined by “Units” resource.</Units>
				<Description>The minimum value measured by the sensor since power ON or reset</Description>
			</Item>
			<Item ID="5602">
				<Name>Max Measured Value</Name>
				<Operations>R</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Optional</Mandatory>
				<Type>Float</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units>Defined by “Units” resource.</Units>
				<Description>The maximum value measured by the sensor since power ON or reset</Description>
			</Item>
			<Item ID="5603">
				<Name>Min Range Value</Name>
				<Operations>R</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Optional</Mandatory>
				<Type>String</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units>Defined by “Units” resource.</Units>
				<Description>The minimum value that can be measured by the sensor</Description>
			</Item>
			<Item ID="5604">
				<Name>Max Range Value</Name>
				<Operations>R</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Optional</Mandatory>
				<Type>Float</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units>Defined by “Units” resource.</Units>
				<Description>The maximum value that can be measured by the sensor</Description>
			</Item>
			<Item ID="5701">
				<Name>Sensor Units</Name>
				<Operations>R</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Optional</Mandatory>
				<Type>String</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units></Units>
				<Description>Measurement Units Definition e.g. “Cel” for Temperature in Celsius.</Description>
			</Item>
			<Item ID="5605">
				<Name>Reset Min and Max Measured Values</Name>
				<Operations>E</Operations>
				<MultipleInstances>Single</MultipleInstances>
				<Mandatory>Optional</Mandatory>
				<Type>Opaque</Type>
				<RangeEnumeration></RangeEnumeration>
				<Units></Units>
				<Description>Reset the Min and Max Measured Values to Current Value</Description>
			</Item>
		</Resources>
		<Description2></Description2>
	</Object>
</LWM2M>
```

## 4.3. Smart Objects - Starter Pack

This first IPSO Smart Object Guideline describes 18 Smart Object types, including a temperature sensor, a light controller, an accelerometer, a presence sensor, and other common sensor and actuator types representing a variety of use case domains. It is intended as a “Starter Pack” and example of how IPSO Smart Objects can be built to address some application specific use cases.

This first object set is intended to be used as a starting place from which to build more objects and object sets, in order to address vertical application segments and new functional requirements for Smart Objects. The IPSO Alliance is committed to making it easy for people to create new objects based on their use case needs, while promoting reusable and cross-domain standards to as great an extent as is practical.

    
| Object 		| Object ID     |
|:----------------------|:-----:|
|    Digital 			|  3200 |
|    Digital Output  	|  3201 |
|    Analogue Input  	|  3202 |
|    Analogue Output 	|  3203 |
|    Generic Sensor  	|  3300 |
|    Illuminance Sensor |  3301 |
|    Presence sensor 	|  3302 |
|    Temperature Sensor |  3303 |
|    Humidity Sensor 	|  3304 |
|    Power Measurement 	|  3305 |
|    Actuation 			|  3306 |
|    Set Point 			|  3308 |
|    Load Control 		|  3310 |
|    Light Control 		|  3311 |
|    Power Control 		|  3312 |
|    Accelerometer 		|  3313 |
|    Magnetometer 		|  3314 |
|    Barometer  	    |  3315 |

## 4.4. Smart Objects - Expansion Pack

To complement the initial set of objects, this new IPSO Smart Object Expansion Pack was created. The Expansion Pack covers a new set of 16 Common Template sensors, 6 Special template sensors, 5 Actuators and 6 Control switch types.  

Some of the new objects are generic in nature, such as voltage, altitude or percentage, while others are more specialized like the Color Object or the Gyrometer Object. New Actuators and Controllers are defined such as timer or buzzer and Joystick and Level. All of these objects were found to be necessary on a variety of use case domains.

    
| Object 		| Object ID     |
|:----------------------|:-----:|
|Voltage		| 3316|
|Current		| 3317|
|Frequency		| 3318|
|Depth		| 3319|
|Percentage		| 3320|
|Altitude		| 3321|
|Load		| 3322|
|Pressure		| 3323|
|Loudness		| 3324|
|Concentration 		| 3325|
|Acidity		| 3326|
|Conductivity		| 3327|
|Power		| 3328|
|Power Factor		| 3329|
|Rate		| 3346|
|Distance		| 3330|
|Energy		| 3331|
|Direction		| 3332|
|Time		| 3333|
|Gyrometer		| 3334|
|Color 		| 3335|
|GPS Location 		| 3336|
|Positioner 		| 3337|
|Buzzer		| 3338|
|Audio Clip		| 3339|
|Timer		| 3340|
|Addressable Text Display		| 3341|
|On/Off Switch		| 3342|
|Push Button		| 3347|
|Level Control		| 3343|
|Up/Down Control		| 3344|
|Multistate Selector		| 3348|
|Multiple Axis Joystick		| 3345|

## 4.5. Smart Objects - Application Specific Objects

Apart from the basic object sets IPSO provide Objects sets from reusing existing Resources tailored to other use cases.
Anyone can create new Objects from various Resources.


## 5. References

[1] 	The Constrained Application Protocol (CoAP):  https://tools.ietf.org/html/rfc7252

[2] 	Open Mobile Alliance Ltd., Lightweight Machine to Machine Technical Specification, 2014.

[3] 	R. Fielding, J. Gettys, J. Mogul, H. Frystyk, L. Masinter, P. Leach and T. Berners-Lee, „Hypertext Transfer Proocol - HTTP/1.1” RFC 2616, June 1999.

[4]		Smart Objects Design Guidelines: https://github.com/IPSO-Alliance/SmartObjectGuidelines/tree/master/SmartObjectDesignGuide 

[5] 	Constrained RESTful Environments (CoRE) Link Format:  https://tools.ietf.org/html/rfc6690
