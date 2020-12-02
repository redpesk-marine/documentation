# Architecture

## API based Micro Service

The application architecture is directly inherited from [AGL](http://automotivelinux.org) and is based on latest version of the [application framework](https://iot.bzh/en/publications/40-2020/107-agl-amm-july20-appfw-perfs).

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

For further details on security model check [AGL security blueprint](http://iot.bzh/download/public/2018/Security/AGL-Security-blueprint_v5.0.0.pdf)
