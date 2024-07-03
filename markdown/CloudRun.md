# CLOUD RUN

### General 

Can be: 
1) container 
2) source-based deployment that will build the container for you (integration with Google Cloud Buildpacks)

Two Deployment Options
1) **Cloud Run Service**: used to run code responding to web requests or events ie. Websites, APIs and microservices, streaming data processing
2) **Cloud Run Job**: used to run code performing a job + then quit when job is done ie. scripts (ie. data migration, etc.), scheduled operational tasks, etc.  

Heavy integration with other GCP resources (for ie. data storage, error reporting, logging, service identity, ci/cd, etc.)

### Cloud Run Services

Cloud Run revision: 
Cloud Run Revision: specific, immutable version of the containerized application, good for version control, traffic management (see below), etc. 
Each Cloud Run revision is linked to a service account, can be used to authenticate with other GCP APIs


A CR Service exposes a unique HTTPS endpoint (code must listen on TCP port for incoming HTTP requests): 
    - subdomain of *.run.app or custom domain 
    - manages TLS 
    - support for Websockets, HTTP/2, gRPC

Autoscaling: 
    - CPU allocation: always on -> means can handle increased CPU utilization outside requests 
    - can limit max number of instances 
    - can set a minimum amount of active instances to prvent scale-to-0 and increased response-time for first few requests 

Traffic Management: 
    - new deployment -> produces new immutable revision 
    - traffic can be split to different revisions for a gardual rollout 

Access Control: 
By default (? #TODO), CR Services are reachable from the internet 
  
Private access can be done through: 
    - 1) Cloud IAM access policy 
    - 2) Ingress settings to restrict network access (to create internal VPC-only services)
    - 3) Cloud Identity-Aware Proxy 

Caching: 
Can place CDN (ie. Cloud CDN) in front of Cloud Run Service for caching from edge location closer to clients


#### Pricing Models 

1) Request-based: pay only per CPU for requests that are allocated
2) Instance-based: pay for entire lifetime of instance, CPU always allocated 


#### Volume Mounts 
Can flush local buffers or persist local data to external data store by configuring Cloud Run to trap SIGTERM signal before shutting down 

### Cloud Run Jobs 

A CR job can be used for scripts, etc. Can be recurring, or part of a workflow 

Array job: run jobs in parallel, used to run jobs split into multiple independent tasks