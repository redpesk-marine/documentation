# Introduction

This project targets a production ready marine grade Linux open platform. Its application and security model derives directly from Automotive Grade Linux[^1] efforts, when production readiness and long term support relies on IoT.bzh continuous integration redpesk[^2] suite.

## Goal

Provide a modern open platform dedicated to next generation of marine embedded applications. The project is a clean break forward from existing Linux maritime projects and aims at providing a clean and modern foundation that will foster development of next generations of maritime services targeting not only leisure sailors but also professional boats with services focus on: navigation/routing, security, energy saving, predictive maintenance and last but not least on a longer term: partial to full autonomous boat.

### Primary goals includes:
* Out of the box support of core marine services as:
  * Signalling: CAN, N2K, J1939, CanOpen, Modbus, ...
  * Cloud Connectivity: 3/4G, Wifi, Bluetooth, ...
  * Chart: S57,S101,Raster, ....
  * Routing: autopilot, motorcontrol, ...
  * ...
* Hardware reference boards
  * Community: Raspberry, Mino, ...
  * Professional: IMX6, STM32MP1, IMX8, RCAR,
  * ...
* Open Software architecture
  * Builtin Cybersecurity model
  * API centric and Microservice based architecture
  * Allow final solution to mix OpenSource and proprietary softwares
  * Long term support targeting 10 years or more
  * ...


### Out of Scope:

Porting existing/legacy softwares or pre-integrating hardware may obviously have values for the community and should obviously remain possible. Nevertheless this is not the focus of this project.

Are excluded from goals:
 * Improving/rewriting new version of existing well known maritime softwares (OpenCpn, QtVlm, zyGrib, SignalK, ...)
 * Creating a new pre-integrated marine distributions (OpenPlotter, Marinux, SkipperOS,  ).
 * Pre-configure a specific distro with a specific set of hardwares (Bareboat, BoatPC, ...)



# Architecture

## API based Micro Service

The application architecture is directly inherited from AGL[^1] and is based on latest version of the application framework[^3].

Within Automotive Grade Linux, each micro service exposes an API that can be requested independently from used message transport layer (REST, UnixSocket, RAM, ...) and from its location (edge, cloud). Application and cybersecurity protection is done through a mixt of kernel mandatory access (SMack, SELinux) and Access control data base as Cynara.

Microservice based architecture are very flexible. They typically allow to split or group service within the same or different devices depending on production requirements. From a developer point of view the fact that a service run into an external 'gateway' or within the same SOC is 100% transparent. Obviously service location still impacts performances or security model, but the flexibility provided by the architecture allows to run the same code on different implementation (i.e. low cost versus full feature system).

![general micro service architecture](images/micro-service-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

Previous graphic represents a full feature system. An entry cost system could use the same services with only one gateway and without any display. On the other hand, high-end systems may require multiple screens (chart-table, cabin, cockpit, ...). Note that the architecture also represents a Linux microcontroller dedicated to critical services. Those critical/safety services may either run in a fully external device based on a STM32 or equivalent device, or directly within the M5-7 microcontroller integrated within an Imx8 or Rcar SOC.

## Cyber-Security

Redpesk@SEA relies on the latest version of Automotive Linux security models.

At installation time, not only is each packet signature verified, but each checksum file contain is also controlled. Services are installed and ran with minimal required privileges. As redpesk is a package based distribution, it is possible to update atomically services.

At run time microservices are protected with a Linux MAC (Mandatory Access Control) either Smack or SeLinux. Then, access to the service is granted by Linux kernel, the application framework applies a fine grade control using Cynara database.

When talking with the cloud or an external device (I.e. phone, tablet) the application framework switches from kernel security to Internet security and uses oAuth2 and OpenIDconnect to request authentication/authorization tokens that will later replace MAC token as input to the Cynara access control data base.

![micro-service security model](images/smack-token-security-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

For further details on security model check AGL security blueprint [^4]

# Core Services

While Redpesk@SEA is fully open an may potentially support any kind of services. Most user expect some core services to be provided "out-of-the-box". This list of core services may expend in the future, but as today we expect to ship with default opensource version following components:

## Signaling

Automotive Linux support in its current version CAN and J1939. For Redpesk@SEA, IoT.bzh already added support for CanOpen and Modbus as well as as a reverse engineered version of NMEA2000[^5] based on Canboat[^6] and other works.

Signaling split into low level binding that are in charge of decoding a binary message (N2K, CanOpen, Modbus, ...) into a high level structure that is easy to process by an application. Second level is handled by the signal composer that is in charge of composing message to build a functional signal when the boat moved more than 300m. The goal is this model is to reduce as close as possible from the acquisition the number of processed messages.

![micro-service signaling model](images/signalling-service-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

For further details on signaling model check[^7]

## Chart & Routing

Redpesk@SEA aims at providing core low level chart service. A full UI as OpenCPN or QtVLM remains out of scope. Nevertheless we aim at providing a strong chart core set of chart services to help that the community to easily develop multiple user interfaces targeting the difference class of maritime users (leisure, fisherman, researcher, ship-yard, harbour, ...).

In a first run, chart service should be able to provide a vector tile service compatible with Mapbox. Chart service should support both a realtime service to serve tiles on demand to UI clients through a standard set of APIs, as well as an out of band service to translate with GDAL or equivalent technology charts from their delivery format(S57,S101,...) into a set of vector tiles compatible with Mapbox vector tiles format[^8]

We also target a basic "safe routing" service. This service might leverage H3 Uber’s Hexagonal Hierarchical Spatial Index [^9] to provide an electronic version of "second captain" that permanently controls if current boat direction is safe or not. The initial version might be limited to static information like maritime charts sounding information or AIS history, nevertheless in a second version we should also leverage dynamic information as Radar, realtime sounding, ...

![micro-service chart model](images/chart-service-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

## Cloud Connectivity

Exchanging data with Internet for both incoming and outgoing streams in a secure manner is critical to support many modern use cases. This secure connection is required to support both external devices (phone, tablet) connecting directly on a boat gateway, or 4G/Satellite connectivity with a cloud service.

As by definition boat can get far enough from ground antenna to loose connectivity, it is key to support connection break down as well as stream selection to limit satellite communication cost.

As cloud connectivity, the initial implementation proposes a mechanism not only to secure stream from/to the cloud, but also a standard option to stage data on the edge waiting for adequate connectivity to be available.

![micro-service cloud model](images/cloud-service-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

## Native Graphic

While not everyone requires native graphic support, anyone who needs to support an embedded screen to display UI will need some form of graphical support.

For remote UI, HTML5 is the magic wand. Unfortunately for native screen support, Wayland only provides very low level API and needs to be complemented in order to support application developers. In order to support Linux existing UI, the system should support XDG as well as some hight level compositor foundation as WLRoot, Fluter or others. While no compositor will ever support every use cases, reference implementation should support one that enable people to get native UI working almost our of the box.

## Monitoring

Monitoring is a key requirement for any long term supported system. Monitoring should be able to support multiple use cases as:

* Enabling developers to trace requests in realtime when debugging
* Collecting long term data track when doing final in situation QA test
* On line data collection to track security break, functional errors, usage patterns, ...

Redpesk@SEA inherits from Automotive Linux application framework monitoring capabilities, it can introspect service API, hook incoming/outgoing messages and support a standard monitoring of Linux general behavior (CPU, RAM, Network, ...). All collected data can either be displayed in realtime (debug case) or stored in a timeserie database for further use with standard tools as Graphana or others.

![micro-service monitoring model](images/monitoring-service-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

## Multimedia

Audio requirement for Automotive is probably too complex for maritime usage and redpesk@sea might only use a subset of AGL audio system. Nevertheless some key functionalities as zones, roles, priorities, ... may soon become hard requirements. At minimum we target:

* a service to play basic multimedia stream as MPD *(Multimedia Player Daemon)*
* a priority service to play emergency sounds *(anchor, AIS, flooding, ...)*
* support of advanced hardwares (MOST, Analog Device, DSP, ...)
* support of sound short cut for safety/emergency sound
* ramp up/down when moving from one stream to an other

In order to minimize the impact on existing Linux applications, the system should provide an ALSA interface for audio stream and use an independent set of API to manage privilege and security policies.

![micro-service audio model](images/audio-service-archi-Intro.png){:: style="margin:auto; display:flex; max-width:80%;"}

# How to contribute

Contribute to the project by joining us on `matrix.org` in our Redpesk@ Marine dedicated room: 
`#redpesk@sea:matrix.org`

<br />
<br />
<br />

[^1]: http://automotivelinux.org
[^2]: http://redpesk.bzh
[^3]: https://iot.bzh/en/publications/40-2020/107-agl-amm-july20-appfw-perfs
[^4]: http://iot.bzh/download/public/2018/Security/AGL-Security-blueprint_v5.0.0.pdf
[^5]: https://www.nmea.org/Assets/20090423%20rtcm%20white%20paper%20nmea%202000.pdf
[^6]: https://github.com/canboat/canboat
[^7]: https://iot.bzh/en/publications/32-2018/92-updated-overview-of-agl-signaling
[^8]: https://docs.mapbox.com/vector-tiles/specification/
[^9]: https://eng.uber.com/h3/

