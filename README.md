## About
This project is an integration of several key open-source software required for annotation and segmentation of DICOM images. The following software is used in this project:

 1. [dcm4chee-arc](https://www.dcm4che.org/)
 2. [OHIF Standalone Viewer](https://github.com/OHIF/Viewers)

## Getting Started
There are a few prerequisites for this project:

 1. [Docker](https://www.docker.com/get-started)
 2. [Node.js and npm](https://nodejs.org/en/)
 3. [Docker Machine](https://docs.docker.com/machine/install-machine/)
 4. [Virtual Box](https://www.virtualbox.org/wiki/Downloads)
 
### Docker-machine creation:
A docker machine instance can be created using the following command :
```
docker-machine create --driver virtualbox {Node name}
```
To access the created docker-machine :
 
 ```
 docker-machine ssh {Node name}
 ```
 
### Docker swarm creation:
```
$ docker swarm init --advertise-addr <NODE-IP>
```
Eg: 

```
$ docker swarm init --advertise-addr 192.168.99.100
Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```
Follow the instructions in the code block to add worker nodes.


### The basic deployment steps are as follows:

 1. Create a Docker swarm with 3 nodes. Name them **node1, node2 , node3 and node4**.
 
 2. On *node1* , run the following commands :
    ```
    cd /opt
    mkdir dcm4chee-arc
    cd dcm4chee-arc
    mkdir ldap
    mkdir slapd.d
    cd ~
    ```
 3. On *node2* , run the following commands:
 ```
    cd /opt
    mkdir dcm4chee-arc
    cd dcm4chee-arc
    mkdir db
    cd ~
 ```
 4. On *node3* , run the following commands:
  ```
    cd /opt
    mkdir dcm4chee-arc
    cd dcm4chee-arc
    mkdir wildfly
    mkdir storage
    cd ~
 ```
 5. On *node1* , clone this repository. 
 ```
 git clone https://github.com/kaiser-team/dcm4che-docker.git
 ```
 6. Go into the cloned reporsitory and run :
 ```
 docker stack deploy -c docker-compose.yml dcm4chee
 ```
Upon succesful deployment, DCM4CHEE will be on http://[docker-machine-ip]:8080/dcm4chee-arc/ui2. 

To run xnat on Node:  

1. First download docker-compose using the following commands on node4:
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
2. After docker-compose has been installed, clone the following respoitory : 
```
git clone https://github.com/kaiser-team/xnat-docker-compose.git
```
3. CD into the xnat-docker-compose directory and run :
```
cd xnat-docker-compose
docker-compose up
```

## Uploading data

You can use common C-STORE methods such as storescu, storescp or the dicomWeb module to upload data into dcm4chee. Be sure to set up the correct AE Titles within dcm4chee. You can do this by going to the ui of your local dcm4chee and then navigating to **Configuration** in the hamburger menu icon.

## FAQ 

### Questions regarding the build process' time
The build process may run up to 300 seconds, when executing the production build. This is normal, as the OHIF Viewer is a Progressive Web Application that converts all modules to static minified assets using Webpack. It performs code-splitting and optimizes assets' build structure within the Docker container it creates, so that the website can run on any lightweight browser that can compile JavaScript. For quick prototyping, we recommend the development build method.

### Questions regarding warnings about unmet peer dependencies
The Viewer has many package.json files that list multiple packages that need to be installed. It uses [**lerna**](https://github.com/lerna/lerna) to manage multiple package files. While the build process is occurring, many packages that requires other packages may be installed first, cause them to throw unmet peer dependency warnings. As such, there are no missing packages that cause the build process to fail for this project. Please refer to the [OHIF Viewer repository](https://github.com/OHIF/Viewers) if you have further issues.
