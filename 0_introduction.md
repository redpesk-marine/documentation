# Introduction

This project targets a production ready marine grade Linux open platform. Its application and security model derives directly from [Automotive Grade Linux](http://automotivelinux.org) efforts, when production readiness and long term support relies on IoT.bzh continuous integration [redpesk](http://redpesk.bzh) suite.

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
  * Allow final solution to mix open source and proprietary softwares
  * Long term support targeting 10 years or more
  * ...

### Out of Scope:

Porting existing/legacy softwares or pre-integrating hardware may obviously have values for the community and should obviously remain possible. Nevertheless this is not the focus of this project.

Are excluded from goals:
 * Improving/rewriting new version of existing well known maritime softwares (OpenCpn, QtVlm, zyGrib, SignalK, ...)
 * Creating a new pre-integrated marine distributions (OpenPlotter, Marinux, SkipperOS,  ).
 * Pre-configure a specific distro with a specific set of hardwares (Bareboat, BoatPC, ...)
