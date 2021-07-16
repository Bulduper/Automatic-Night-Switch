# DOCKER & NODE-RED INSTALLATION
A good explanation on the topic of docker: https://youtu.be/eGz9DS-aIeY

This document assumes that the host OS for the docker is **Ubuntu Linux**.
Ubuntu Linux may be installed as the main OS or using the virtualization technology (VM).
In the case of this documentation, the following Ubuntu OS version was used:
```
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.2 LTS
Release:	20.04
Codename:	focal
```

1. Install docker

First check if any updates are available:
```
$ apt-get update
```
If any terminal command throws permission error, use ``sudo`` before. This will ask for user password and apply admin permissions.

Then to install docker type:
```
$ sudo apt install docker.io -y
```
To test if the installation of docker was successful try:
```
$ sudo docker run hello-world
```
2. Create NodeRed container

Official support on installing NodeRed under docker: https://nodered.org/docs/getting-started/docker
Make sure you are in home directory ``$ cd .``.
Create a folder for the node-red specific files ``$ mkdir node-red`` and go to that folder.

To install the NodeRed container type:
```
$ docker run -it -p 1880:1880 -v /home/{username}/node-red:/data --name mynodered nodered/node-red
```
The terminal should perform the installation and let you know what is the NodeRed server address, e.g.: ``Server now running at http://127.0.0.1:1880/``.
 Go to the address or type ``localhost:1880`` in your web browser. NodeRed UI should appear.

3. Start/Restart/Stop NodeRed 

To start/restart/stop the server type:
```
$ sudo docker [start/restart/stop] mynodered
```
# MODULES INSTALLATION
In order to extend the functionality of NodeRed service additional modules can be installed.
Go to *Menu tab* and then to *Manage palete*...

![module installation](https://i.ibb.co/kKPw3yX/Screenshot-from-2021-07-14-16-07-59.png)
Type the keywords which describe the addon you need and click *install*.
After installation a new palette should appear in the left section tab:

![new module](https://i.ibb.co/2qC2f3X/Screenshot-from-2021-07-14-16-13-56.png)
# AUTOMATIC NIGHT SWITCH EXAMPLE
In order to present some of the capabilities of NodeRed an example has been designed. The ***node-red-contrib-ewelink*** module is required.

## Working principle
The "night switch" used in this example is an IoT device which consist of an ESP8266 $\mu$C and a relay. It can switch on and off any low power mains voltage device such as a bedlamp. 

![SONOFF BASIC](https://gotronik.pl/images/sonoff_basic_3.jpg)
The user should be able to type in his current address (location) and according to it the current part of a day would be obtained. In case of a nighttime the switch would turn on and let the current flow to the device e.g. bedlamp. In case of a daytime the switch would go off.

## The flows

The whole process consists of two NodeRed flows.

### The main flow

This flow is triggered every minute. It delegates geocoding to the other flow. Then the [api](https://sunrise-sunset.org/api) request is send to obtain the time of sunrise and sunset for the location. Current time is compared with sunrise and sunset time and hence the time of day is obtained.
Based on that time of day, a specific (on/off) payload is sent to the device node.

![The main flow](https://i.ibb.co/KFvd8sv/Screenshot-from-2021-07-16-10-41-03.png)

### Geocoding flow
In this flow the geocoding is implemented. A user can go to local */location* page and specify the address for which the today's sunrise/set time will be obtained.

![Location](https://i.ibb.co/23pnBr1/Screenshot-from-2021-07-16-11-02-55.png)

Right after submitting the address an online [free API](https://developer.mapquest.com/documentation/open/geocoding-api/) is used to perform the geocoding and return the lattitude and longitude for the specified location.

![The geocoding flow](https://i.ibb.co/YTkvhG0/Screenshot-from-2021-07-16-10-57-49.png)

The lat & lng become global variables, so the main flow can later use them.

## Ewelink node configuration
There are several nodes included in the ewelink module. The node used in this example is *Power State Node*. It takes *msg.payload* as input. You must provide either "on" or "off" in the payload.

The node outputs current status and state of the device.

The device - node configuration is very easy.

First insert the credentials of your ewelink account by clicking on the *pencil icon*: 

![Credentials](https://i.ibb.co/9WBN5vR/Screenshot-from-2021-07-16-11-05-48.png)

Then insert the device name and ID. All device info can be checked on an ewelink website or ewelink mobile app.

![Power state node](https://i.ibb.co/Nx3dS2k/Screenshot-from-2021-07-16-11-05-31.png)


