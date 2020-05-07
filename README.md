## About
This project is an integration of several key open-source software required for annotation and segmentation of DICOM images. The following software is used in this project:

 1. [dcm4chee-arc](https://www.dcm4che.org/)
 2. [OHIF Standalone Viewer](https://github.com/OHIF/Viewers)
 3. [XNAT](https://www.xnat.org/) (In-Progress)
 4. [CLARA](https://developer.nvidia.com/clara)

## Getting Started
There are a few prerequisites for this project:

 1. [Docker](https://www.docker.com/get-started)
 2. [Node.js and npm](https://nodejs.org/en/)

### The basic installation steps are as follows:

 1. Clone this repository
 2. `cd dcm4chee-docker`
 3. Clone the [OHIF Viewer](https://github.com/OHIF/Viewers)

At this point, there are two ways to build the project - for production and development:

#### For production:

 1. Run `docker-compose -p dcm4chee up -d`
The `d` flag is optional. It provides terminal control and lets docker-compose run in the background.

#### For development:
We recommend this step if you are making rapid changes to the Viewer.

 1. Checkout branch `sansViewer` of this repository. It contains docker-compose that will bring up all dcm4chee services, but not the OHIF Viewer.
 2. Run `docker-compose -p dcm4chee up -d`
 3. `cd Viewers`
 4. Run `npm i` or `yarn install` to install all packages. This will take some time.
 5. Run `npm run dev` or `yarn run dev` to start the development server.

At this point, the following services will be running at the following ports:

1. dcm4chee_arc: https://www.localhost:8080/dcm4chee-arc/ui
2. OHIF Viewer: https://www.localhost:80 for the production build, or https://www.localhost:3000/ for development build

## Uploading data

You can use common C-STORE methods such as storescu, storescp or the dicomWeb module to upload data into dcm4chee. Be sure to set up the correct AE Titles within dcm4chee. You can do this by going to the ui of your local dcm4chee and then navigating to **Configuration** in the hamburger menu icon.

## FAQ 

### Questions regarding the build process' time
The build process may run up to 300 seconds, when executing the production build. This is normal, as the OHIF Viewer is a Progressive Web Application that converts all modules to static minified assets using Webpack. It performs code-splitting and optimizes assets' build structure within the Docker container it creates, so that the website can run on any lightweight browser that can compile JavaScript. For quick prototyping, we recommend the development build method.

### Questions regarding warnings about unmet peer dependencies
The Viewer has many package.json files that list multiple packages that need to be installed. It uses [**lerna**](https://github.com/lerna/lerna) to manage multiple package files. While the build process is occurring, many packages that requires other packages may be installed first, cause them to throw unmet peer dependency warnings. As such, there are no missing packages that cause the build process to fail for this project. Please refer to the [OHIF Viewer repository](https://github.com/OHIF/Viewers) if you have further issues.