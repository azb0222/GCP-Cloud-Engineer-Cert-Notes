# CLOUDBUILD 

CloudBuild YAML file contains configuration steps, env variables, artifacts, etc. for the build. 

### Build Config 

build config file has instructions to build, package, and push artifacts (ex. Docker images, Java archives, etc.), can be YAML or JSON 

#### Build Steps  

each build step -> each action. Each build step is a Docker container as an instance of docker run (runs a command in new container, pulls the image if needed, and starts container). 

Cloud Build executes all steps of a build on the same machine in a serial fashion 

Cloud builders: container images with common languages and tools. Types of cloud builders: 
1) publically available images ie. ubuntu 
2) Cloud Build builder images: pre built images (gcr.io/cloud-builders/...) (ie. gcr.io/cloud-builders/docker, gcr.io/cloud-builders/gcloud)
3) Community-contributed builders: download source code from https://github.com/GoogleCloudPlatform/cloud-builders-community and build the image (ie. docker-compose, helm) 
4) Custom builder: custom container image with your source 


```
steps: 
- name: 'ubuntu' //specify a cloud builder 
  args: ['echo', 'hello world'] //specify command to run within image, arguments are passed to the tool within builder. if builder has entrypoint, the args are arguments to that entry 
```




### Pools + Private Pool 
Each build runs in a secure, hosted environment with public interent access. 
Each build runs its own worker, isolated from other workloads. 
Builds can be customized to increase size, machine type, more disk space, etc.

Two types of pools: 
1) Private Pools: private, dedicated pools of workers for greater customization, can access resources in a private network 
2) Default Pool: limits placed on env customization

```
steps: 
    - name
```

## Resources 
https://cloud.google.com/build/docs/overview