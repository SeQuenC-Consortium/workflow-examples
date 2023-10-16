# Example implementation: On-Demand Push-Based OpenTOSCA Python Server

## Overview

This example implementation is used to show the basic functionality of the implemented on-demand deployment functionality of the workflow modeler, as well as of the deployment-view-plugin for Camunda.

## Prerequisites
For the set-up, docker should be available and running.

### Manually

For the manual set-up both npm (tested with [9.5.1](https://www.npmjs.com/package/npm/v/9.5.1)) and maven (tested with version 3.6.1 and JDK 19) are necessary to build and run applications.
Both Winery and OpenTOSCA need to be set up and running. Tested with a set-up based on the [opentosca-docker repository](https://github.com/OpenTOSCA/opentosca-docker).
Make sure no conflicts with ports arise and that port 10123 is forwarded by the dind container. A pre-adjusted docker-compose is available on the [demo/view-plugin branch](https://github.com/OpenTOSCA/opentosca-docker/tree/demo/view-plugin). However other set-up, such as the configuration of the environment, still needs to be completed manually.

These changes were made to the docker-compose.yml of the opentosca-docker repository when compared to the current main branch (11.10.23) (port allocations were tested on a Windows Machine):
  * Winery: Under winery, change ports from '8080:8080' to '8079:8080'
  * UI: Change WINERY_PORT to 8079
  * DIND: Add 10123:10123 to the list of forwarded ports

Next, pull the [feature 2 branch of the camunda-deployment-view-plugin repository](https://github.com/SeQuenC-Consortium/camunda-deployment-view-plugin/tree/feature/2-visualize-deployment-model) and set up the plugin according to the README.

To avoid port conflicts, launch Camunda on a different port (tested on 8078) by adding the following lines to the default.yml:

```
server:
  port: 8078
```

Similarly, pull the [dev-main branch of the workflow-modeler repository](https://github.com/SeQuenC-Consortium/workflow-modeler/tree/dev-main) and execute it according to the given README. 

### Docker

A complete docker-compose including changes and containers for both the workflow-modeler is available at the [demo-plugin branch of the opentosca-docker repository](https://github.com/OpenTOSCA/opentosca-docker/tree/demo/view-plugin)
For this set-up to work, similarly to the manual setup, you have to clone the [dev-main branch of the workflow-modeler repository](https://github.com/SeQuenC-Consortium/workflow-modeler/tree/dev-main) and the [dockerimage branch of the camunda-deployment-view-plugin repository](https://github.com/SeQuenC-Consortium/camunda-deployment-view-plugin/tree/5-dockerimage). Build the dockerfiles by using

```
docker build -t workflow-modeler:local .
```
and 
```
docker build -t camunda-deployment-view-plugin:local .
```
respectively.

Set up the remainder of the repro as instructed in the README provided with it.

## Set-Up
**NOTE: When using services, address them using your local IP determined when setting up opentosca-docker instead of localhost, to avoid issues with connections between components**

### Winery
The service-template of the example Python server needs to be set up. For this import the CSAR given as part of the repository into the winery (If this causes issues, retry while enabling override, or just retry multiple times). Open the topology template, and adjust the DockerEngine-Node's properties to work with your machine (Importantly, adjust the Public IP). If there are issues with the deployment artifact of the top node, use the app.py file given as part of this repository and attatch it to the PythonApp Node as a PythonArchiveArtifact.

### Workflow-Modeler
Next, inside the workflow-modeler, configure the endpoints according to your Winery, OpenTOSCA, and Camunda setup. Make sure to avoid using localhost, as it causes issues with docker setups.

In the case you followed our setup, this would mean the following (Replace ${PUBLIC_HOSTNAME} using your ip determined when setting up opentosca-docker):

  * Winery: Change IP and Port to ${PUBLIC_HOSTNAME}:8079 (Port change only necessary in  manual setup)
  * Camunda: Change IP and Port to ${PUBLIC_HOSTNAME}:8078
  * OpenTOSCA: Change Port to ${PUBLIC_HOSTNAME}

Then, load the saved workflow provided in this repository. If everything is set up correctly, the service-template you just created should be attached to a service-task and a connector should also be set up.

## Example execution

### Workflow-Modeler

Create a process in Camunda by selecting *deployment* and choosing *Yes* for on-demand deployment.

### Camunda

Using Camunda, start the process that was just created. Its name is *on* and it can be started from the task list. When prompted for an input, use *demand*. Once the deployment using OpenTOSCA is started, you will see a running instance. The subtasks can be seen by expanding the service task using the corresponding button. Once deployment by Tosca is finished (can take up to 10 minutes), the final task of the subtasks will send the request to the deployed server and log an answer. At this point, it is possible to check the stat of the deployment using the cockpit, which also includes checking of the subprocesses which were created by the workflow modeller.
