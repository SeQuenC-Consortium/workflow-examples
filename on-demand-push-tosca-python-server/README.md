# Example implementation: On-Demand Push-Based OpenTOSCA Python Server

## Overview

This example implementation is used to show the basic functionality of the implemented on-demand deployment functionality of the workflow modeler, as well as of the deployment-view-plugin for Camunda.

## Prerequisites

For the easiest set-up, docker should be available and running. Additionally, both npm (tested with [9.5.1](https://www.npmjs.com/package/npm/v/9.5.1)) and maven (tested with version 3.6.1 and JDK 19) are necessary to build and run applications.
Both Winery and OpenTOSCA need to be set up and running. Tested with a set-up based on the [opentosca-docker repository](https://github.com/OpenTOSCA/opentosca-docker). Make sure no conflicts with ports arise and that port 10123 is forwarded by the dind container.

These changes have to be made to the docker-compose.yml of the opentosca-docker when compared to the current main branch (11.10.23) (port allocations were tested on a Windows Machine):
  * Winery: Under winery, change ports from '8080:8080' to '8079:8080'
  * UI: Change WINERY_PORT to 8079
  * DIND: Add 10123:10123 to the list of forwarded ports

Next, pull the [feature 2 branch of the camunda-deployment-view-plugin repository](https://github.com/SeQuenC-Consortium/camunda-deployment-view-plugin/tree/feature/2-visualize-deployment-model) and set up the plugin according to the README.

To avoid port conflicts, launch Camunda on a different port (tested on 8078) by adding the following lines to the default.yml:

```
server:
  port: 8078
```

Similarly, pull the [dev-main branch of the workflow-modeler repository](https://github.com/SeQuenC-Consortium/workflow-modeler/tree/dev-main) and execute it according to the given README. Configure the endpoints according to your Winery, OpenTOSCA, and Camunda setup. Make sure to avoid using localhost, as it causes issues with docker setups.

In the case you followed our setup, this would mean the following (Replace ${PUBLIC_HOSTNAME}):

  * Winery: Change IP and Port to ${PUBLIC_HOSTNAME}:8079
  * Camunda: Change IP and Port to ${PUBLIC_HOSTNAME}:8078
  * OpenTOSCA: Change Port to ${PUBLIC_HOSTNAME}


  
## Set-Up

### Winery
The service-template of the example Python server needs to be set up. For this import the CSAR given as part of the repository into the winery. Open the topology template, and adjust the DockerEngine-Node's properties to work with your machine. If there are issues with the deployment artifact of the top node, use the app.py file given as part of this repository.

### Workflow-Modeler
Next, inside the workflow-modeler, load the saved workflow provided in this repository. If everything is set up correctly, the service-template you just created should be attached to a service-task and a connector should also be set up.

## Example execution

### Workflow-Modeler

Create a process in Camunda by selecting *deployment* and choosing *Yes* for on-demand deployment.

### Camunda

Using Camunda, start the process that was just created. Its name is *on* and it can be started from the task list. When prompted for an input, use *demand*. Using the cockpit, the process can be supervised. Once the deployment using OpenTOSCA is started, you will see a running instance. The subtasks can be seen by expanding the service task using the corresponding button. Once deployment by Tosca is finished, the final task of the subtasks will send the request to the deployed server and log an answer.
