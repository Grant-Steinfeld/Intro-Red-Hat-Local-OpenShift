
# Deploy model-serving microservices from the Model Asset Exchange(MAX) on Red Hat MiniShift

> This tutorial is based off of an OpenShift cloud tutorial entitled:
> [`Deploy deep learning models on Red Hat OpenShift on IBM Cloud` ](https://developer.ibm.com/tutorials/deploy-a-model-asset-exchange-microservice-on-red-hat-openshift/)


## Learning objective

By completing this introductory tutorial, you learn how to deploy a 
[deep learning microservice from the Model Asset Exchange MAX](https://developer.ibm.com/series/create-model-asset-exchange/) locally on your laptop or desktop compupter by using Red Hat MiniShift.

Upon completion of the tutorial, you know how to use the MiniShift web console or MiniShift Container Platform command-line interface aka `oc`  to:

* Create a new project
* Deploy a model-serving microservice from a public container image on Docker Hub
* Create a route that exposes the microservice to the public

## Prerequisites

To follow this tutorial, you must have:

Minishift installed locally on your laptop/computer

## Estimated time

It should take you approximately 20 minutes to complete this tutorial. The tutorial modules are:

[Setup](#tutorial-setup)





## Deploy a deep learning model-serving microservice on Red Hat MiniShift

In this tutorial, you will deploy a model-serving microservice from the [Model Asset Exchange](https://developer.ibm.com/exchanges/models/) 

on Red Hat MiniShift using the MiniShift web console or the MiniShift CLI `oc`. 

You can complete both parts A & B or only one part.

### A) Deploy using the OpenShift web console

In this first part of the tutorial, you deploy the [MAX-Object-Detector](https://developer.ibm.com/exchanges/models/all/max-object-detector/) model-serving microservice,
which can be used to identify objects in pictures, as shown in this 
[demo](http://max-object-detector.max.us-south.containers.appdomain.cloud/app/).

1.  Open the MiniShift web console.
The catalog browser is displayed.

1.  From the drop-down menu, select the Application console. 


1. A list of your projects is displayed. A project is used in MiniShift to organize related resources.

3.  Create a new project and assign it a name (such as `max-deployments`), a display name, and an optional description.
4.  Open the project.

### Deploy the microservice Docker image

You can deploy Docker images that are hosted in public or private registries. The MAX-Object-Detector Docker image `codait/max-object-detector` is hosted on [Docker Hub](https://hub.docker.com/r/codait/max-object-detector).

1.  On the project overview page, choose Deploy Image.


1.  Select the model-serving Docker image that you want to deploy.

1.  Choose the Image Name radio button to select a public or private repository as the source.
1.  Enter `codait/max-object-detector` as _Image name or pull spec_ 
(or enter the name of another model-serving Docker image 
[from our repository on Docker Hub](https://hub.docker.com/r/codait/)

1.  Click on the `magnifying glass` next to the image name (or press Enter) to load the Docker image’s metadata.
1.  Review the deployment configuration for the selected Docker image.

The _Name_ field is pre-populated using the name of the Docker image. 
MiniShift uses this name to identify the resources being created when the application is deployed.




1.  Deploy the Docker image.

1.  Close the window and open the Overview tab if it is not selected by default. 
The deployment configuration for the Object Detector model-serving microservice is displayed.


After the microservice is deployed, it is only visible internally (at port 5000) to the cluster. 

To expose it to the public, you must create a route.

### Create a route

When you [create a route](https://docs.openshift.com/container-platform/3.11/dev_guide/routes.html) 
in MiniShift, you have the option to expose an unsecured or a secured route.
Because the model-serving miroservice communicates over HTTP, 
you can configure the router to expose an unsecured HTTP connection 
(which is what you’ll do in this tutorial) 

1.  Select Create Route under the NETWORKING section.
1.  Review the configuration settings. The defaults expose an unsecured HTTP 
connection at a generated URL if you leave the hostname empty. 


1.  Create the route. Upon completion of the operation, the public URL for the deployed model-serving microservice is displayed. 

> If an error is displayed indicating that the router rejected the request, shorten the route name. The maximum length of the generated URL (derived by concatenating the route name, the project name, the cluster name, and the router’s domain) likely exceeds the 63 character maximum.

4.  Open the displayed URL to verify that the microservice’s OpenAPI 
specification endpoint can be accessed.

5.  If you have been following this tutorial using the Object Detector,
open the embedded sample application by appending `/app` to the URL 
displayed in your browser, and test the deployed service by submitting an image.


## B) Deploy using the CLI

You can deploy and manage applications using the MiniShift Container Platform CLI `oc`. Before you can use the CLI to deploy the microservice in your cluster, you must log in.

1.  Open the MiniShift web console and copy the login command.

1.  In a web browser, open our MiniShift console.
1.  From the … drop-down menu, select MiniShift web console.

The catalog browser is displayed.

4.  From the avatar drop down, select Copy Login Command.


2.  Log in to your cluster using the OpenShift CLI.

1.  Open a terminal window.
2.  Paste the copied login command.

```
oc login https://... --token=...
```


> Tip: To learn more about a command, run `oc <command> --help`, for example, `oc login --help`.

3.  Create a new project using a name such as `max-deployments-cli`. 

OpenShift uses a project to organize related resources.
```
oc new-project max-deployments-cli
```


> You can list existing projects using `oc projects` or switch between projects using `oc project <project-name>`


### Deploy the Docker image

You can deploy Docker images that are hosted in public or private registries. In this part of the tutorial, you deploy the [MAX-Image-Caption-Generator](https://developer.ibm.com/exchanges/models/all/max-image-caption-generator/) model-serving microservice, which can be used to describe the content of a picture in a sentence, as shown in [this demo](http://max-image-caption-generator-web-app.mybluemix.net/).

1.  Verify that you can access the [`codait/max-image-caption-generator` Docker image on Docker Hub](https://hub.docker.com/r/codait/max-image-caption-generator).

```

oc new-app --search codait/max-image-caption-generator

```


The output should look as follows:

```

Docker images (oc new-app --docker-image=<docker-image> [--code=<source>])
-----
codait/max-image-caption-generator
Registry: Docker Hub
Tags:     latest

```


2.  Deploy the Docker image.

```
oc new-app codait/max-image-caption-generator
```



Review the output and note that the image name `max-image-caption-generator` is used by default to name the generated resources, such as the image stream, the deployment configuration, the service, and the host.

```
--> Found Docker image ... (... days old) from Docker Hub for "codait/max-image-caption-generator"

An image stream tag will be created as "max-image-caption-generator:latest" that will track this image
This image will be deployed in deployment config "max-image-caption-generator"
Port 5000/tcp will be load balanced by service "max-image-caption-generator"
Other containers can access this service through the hostname "max-image-caption-generator"
WARNING: Image "codait/max-image-caption-generator" runs as the 'root' user which may not be permitted by your cluster administrator

--> Creating resources ...
imagestream.image.openshift.io "max-image-caption-generator" created
deploymentconfig.apps.openshift.io "max-image-caption-generator" created
service "max-image-caption-generator" created
--> Success
...
Run 'oc status' to view your app.

```


4.  Query the deployment status.

```
$> oc status




In project max-deployments-cli on server https://...

svc/max-image-caption-generator - ...:5000
dc/max-image-caption-generator deploys istag/max-image-caption-generator:latest
deployment #1 deployed 15 minutes ago - 1 pod

```


### Mangage scales
> You can manually scale out the deployment by increasing the target number of running pods (`oc scale --replicas=2 deploymentconfig max-image-caption-generator`) or [configure autoscaling](https://docs.openshift.com/container-platform/3.11/dev_guide/pod_autoscaling.html).

## To expose the service to the public, you must create a route.

### Create a route

When you [create a route](https://docs.openshift.com/container-platform/3.11/dev_guide/routes.html) in OpenShift, 
you have the option to expose an unsecured or a secured route. 
Because the model-serving miroservice communicates over HTTP, 
you can configure the router to expose an unsecured HTTP connection 
(which is what you’ll do in this tutorial) 

1.  Create a route for the service.

```
oc expose service max-image-caption-generator
```

2.  Retrieve the microservice’s public URL.

```
oc get route max-image-caption-generator
```


Under the `HOST/PORT` column, the generated host name is displayed.
```
NAME                          HOST/PORT                                                          ...     
max-image-caption-generator   max-image-caption-generator-max-deployments-cli...appdomain.cloud  ...
```


3.  Open the displayed URL in your web browser to verify that the microservice is accessible.

4.  Validate that the `/model/predict` endpoint returns the expected result for a picture of your choice.

Note: For some compute-intensive models, calls to the `/model/predict` endpoint might result in [HTTP error 504 (Gateway timeout)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/504) if you use the router’s default configuration. To resolve the issue, increase the router’s timeout value by running `oc annotate route <router-name> --overwrite haproxy.router.openshift.io/timeout=<number-of-seconds>s`.

## Summary

In this tutorial, you learned how to use the OpenShift web console or OpenShift Container Platform CLI to:

Create a new project
Deploy a model-serving microservice from a public container image on Docker Hub
Create a route that exposes the microservice to the public

To learn more about the Model Asset Exchange or how to consume the deployed model-serving microservice in Node-RED or JavaScript, take a look at [Learning Path: An introduction to the Model Asset Exchange](https://developer.ibm.com/series/create-model-asset-exchange/)

You might find the following resources useful if you’d like to learn more about Red Hat Minishift/OpenShift:

## Resources
### Data analytics
[Data and analytics architecture](https://www.ibm.com/cloud/garage/architectures/dataAnalyticsArchitecture)
[Data and analytics solutions](https://www.ibm.com/cloud/garage/architectures/dataAnalyticsArchitecture/alldataanalyticssolutions)
[Newsletter](/newsletters/artificial-intelligence/)

[Model Asset eXchange](https://developer.ibm.com/exchanges/models/)
[Data Asset eXchange](https://developer.ibm.com/exchanges/data/)


[Getting started with Red Hat OpenShift on IBM Cloud](https://cloud.ibm.com/docs/openshift?topic=openshift-getting-started)
[OpenShift Learning Portal](https://learn.openshift.com)
[OpenShift Container Platform CLI reference](https://docs.openshift.com/container-platform/3.11/cli_reference/index.html)


 [#Open Source Data & AI Technologies](https://developer.ibm.com/collections/codait/)


[Artificial intelligence](https://developer.ibm.com/technologies/artificial-intelligence/)

[Data science](https://developer.ibm.com/technologies/data-science/)

[Deep learning](https://developer.ibm.com/technologies/deep-learning/)

[Machine learning](https://developer.ibm.com/technologies/machine-learning/)

[Microservices](https://developer.ibm.com/technologies/microservices/)


[Get an introduction to the Model Asset Exchange on IBM Developer](https://developer.ibm.com/articles/introduction-to-the-model-asset-exchange-on-ibm-developer/)

[ Get started with the Model Asset Exchange](https://developer.ibm.com/tutorials/getting-started-with-the-ibm-code-model-asset-exchange/)


### IBM Developer Globally

[Answers](https://developer.ibm.com/answers/)

[English](//developer.ibm.com/)

[中文](//www.ibm.com/developerworks/cn/)

[日本語](//www.ibm.com/developerworks/jp/)

[Русский](//www.ibm.com/developerworks/ru/)

[Português](//www.ibm.com/developerworks/br/)

[Español](//www.ibm.com/developerworks/ssa/)

[한글](//developer.ibm.com/kr/)


### Social links
Follow Us:
[Twitter](https://twitter.com/IBMDeveloper)

[LinkedIn](https://www.linkedin.com/showcase/developerworks/)

[Facebook](https://www.facebook.com/IBMDeveloper)

[YouTube](https://www.youtube.com/channel/UCUm6InQvGI9-6vo1teGWINA)
