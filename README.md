# Docker_Kubernetes_BlogApp

Microservices are an architectural and organizational approach to software development where software is composed of small independent services that communicate over well-defined APIs. Microservices architectures make applications easier to scale and faster to develop, enabling innovation and accelerating time-to-market for new features.
Docker and Kubernetes are almost synonymous to 'microservices' as they help package and manage the different components of a project/ application, thereby easing up the implementation of a microservices architecture.

The microservices architecture will deploy a Kubernetes cluster with a mongodb server pod fronted with a web admin interface and a pod to run the flask app.

## Pre-Requisites/ Pre-Installation:
1. Docker ([Windows](https://docs.docker.com/desktop/windows/install/) | [Ubuntu](https://docs.docker.com/engine/install/ubuntu/#:~:text=Install%20from%20a%20package&text=Go%20to%20https%3A%2F%2Fdownload,version%20you%20want%20to%20install) | [MacOS](https://docs.docker.com/desktop/mac/install/))
2. Kubernetes ([Windows](https://birthday.play-with-docker.com/kubernetes-docker-desktop/) | [Ubuntu](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) | [MacOS](https://birthday.play-with-docker.com/kubernetes-docker-desktop/))

## File Structure
```
.
|-- README.md
|-- app
|   |-- app.py
|   |-- requirements.txt
|   `-- templates
|       |-- base.html
|       |-- create-post.html
|       |-- edit-post.html
|       `-- home.html
|-- configmap.yaml
|-- deployments.yaml
|-- flask-app-image.dockerfile
|-- secret.yaml
`-- services.yaml
```
The `app` directory contains all the code pertaining to the flask app. You are only required to configure the mongo connection string variables as specified in `app.py`.  
The `flask-app-image.dockerfile` should specify the insructions to assemble the docker image for the flask app.  
The `.yaml` files in the root directory are to specify the kubernetes manifests that will bring up your microservices deployment of the problem statement.

## Tasks
1. MongoDB Server
    1. The `mongo` image publicly available on DockerHub is used. Read through the configuration and other details of the image [here](https://hub.docker.com/_/mongo). The necessary environment variables to be configured are noted.
    2. The `Deployment` for the mongodb server under `deployments.yaml` is created. Remember to configure the ports and setup the environment variables correctly.
    3. Environment variables such as username, password, etc. are sensitive information and are defined as a `Secret`. A `secret.yaml` file is defined to hold the sensitive information required by the mongodb server. You may create a secret _using a configuration file_ and use the secret in your deployment as _an environment variable_.  [Read more](https://newrelic.com/blog/how-to-relic/how-to-use-kubernetes-secrets).
    4. A `Service` for the mongodb server under `services.yaml` is created.

2. Mongo-Express Web Service
    1. The `mongo-express` image is used. The necessary environment variables like before from [here](https://hub.docker.com/_/mongo-express) are noted.
    2. A `configMap` to store the mongodb server url is defined. As above, The configmap to configure the container with environment variables is used. [Read more](https://kubernetes.io/docs/concepts/configuration/configmap/).
    3. A `Deployment` for the mongo-express service under `deployments.yaml` is created and the necessary ports and environment variables are configured (drawn from the secret and configmap).
    4. A `service` for the pod under `services.yaml` is also defined.

3.  Flask WebApp
    1. The image created from the `flask-app-image.dockerfile` is used.
    2. A`Deployment` for the flask app under `deployments.yaml` is created.
    3. A `Service` for the pod in `services.yaml` is also defined.  


## Bringing it all together
Once all the microservices are up and running,
1. Inside the flask-app pod, a python script is written and run to insert records into the mongodb database. Insert into: database = 'blog' and collection = 'posts'.
2. app.py` is run inside the pod. Visit `http://localhost:<port>/` to view the Blog App. The Home Page should display the records inserted into the database in the previous step.
3. You can view the database frontend exposed by mongo-express. To do so, on your browser, navigate to`EXTERNAL_IP:port` exposed by the mongo-express service. 
