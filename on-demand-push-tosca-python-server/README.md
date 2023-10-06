# Example implementation: On-Demand Push-Based OpenTOSCA Python Server

## Overview

This example implementation is used to show the basic functionality of the implemented on-demand deployment functionallity of the workflow modeler, as well as of the deployment-view-plugin for camunda.

## Prerequisites

For the easiest set-up, docker should be available and running.
Both Winery and OpenTOSCA need to be set-up and running. Tested with a set-up based on the [opentosca-docker repository](https://github.com/OpenTOSCA/opentosca-docker).
Next, pull the [dev-main branch of the workflow-modeler repository](https://github.com/SeQuenC-Consortium/workflow-modeler/tree/dev-main) and execute it according to the given README. Configure the end
Similarly, pull the [dev-main branch of the camunda-deployment-view-plugin repository](https://github.com/SeQuenC-Consortium/workflow-modeler/tree/dev-main) and set up the plugin according to the README.

## Set-Up

### Winery
The service-template of the example python server needs to be set-up. For this import the CSAR given as part of the repository into the winery. Open the topology template, and adjust the DockerEngine-Node's properties to work with your machine. Lastly, the deployment artifact for the python server needs to be attached to the PythonApp node. Create a new ArtifactTemplate of type PythonArchiveArtifact and upload the app.py file given in this repository packed inside a .tar.gz. Attach this Artifact to the PythonApp node and save the topology.

### Workflow-Modeler
Next, inside the workflow-modeler, load the saved workflow provided in this repository. If everything is set up correctly, the service-template you just created should be attatched to a service-task and a connector should also be set-up. Now start the on-demand deployment.

### Camunda
Using camunda, start the process that was just created. Using the cockpit, it can be supervised. Once the deployment using OpenTOSCA is finished, the answer to the request dispatched by the service-task can be seen.
