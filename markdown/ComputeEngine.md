## COMPUTE ENGINE

### IAM Roles 
Can apply at project or instance level: 
-  `roles/compute.instanceAdmin.v1`: full control over all Compute Engine resources (instances, instance groups, networking, etc.)
    - if this role is granted at the instance level, user cannot create other instances 
-  `roles/compute.networkAdmin`: only CRUD networking resources. read only access for certs + firewall rules
-  `roles/compute.securityAdmin`: CRUD certs + firewall rules 
-  `roles/compute.viewer`: can get + list Compute Engine resources. cannot read data 


### Instance Groups 
**Instance Group**: group of VMs 
1) managed: identical VMs, created using instance template. can be automatically scaled + load balanced. crashed instance will be rebooted (preferred)
2) unmanaged: different configurations within different VMs within group. you add instances to the unmanaged instance group. 

must create instance template to create instance group 


### distribution 
1) zonal managed instance group
2) regional managed instance group: (reccomended for high resiliency)

### gcloud commands 
`gcloud compute instance-groups managed create <instance-group-name> --base-instance-name <base-instance-name> --template <template-name> --size <number-of-instances>`: create managed group 

`gcloud compute instance-groups unmanaged create <instance-group-name>`: create unmanaged group 

`gcloud compute instance-templates create INSTANCE_TEMPLATE_NAME \
    --machine-type MACHINE_TYPE \
    --image-family IMAGE_FAMILY \
    --image-project IMAGE_PROJECT \
    --boot-disk-size BOOT_DISK_SIZE \
    --boot-disk-type BOOT_DISK_TYPE`: create an instance template 

`gcloud compute instance-groups managed list-instances INSTANCE-GROUP-NAME`: list VMs 

`gcloud compute instance-templates delete INSTANCE_TEMPLATE_NAME`: delete instance template 

`gcloud compute instance-groups (un)managed delete INSTANCE_GROUP_NAME --zone ZONE`: delete instance group 
#TODO: insert more commands here 

## APP ENGINE 

**App Engine**: PaaS focused on deploying applications (not VMs). GCP will manage resources based on some basic resource requirements 

Application -> Service -> Version -> Instance 

*Specific version of your Service (a part of your Application) runs on a GCP managed Instance* 

Types of Instances: 
- Dynamic Instances: Instances can be autoscaled based on traffic. (Autoscaling policy can be triggered based on CPU utilization, monitoring metrics, load balancing, queue based workloads)
- Resident Instances: manually add/remove

Types of Runtime Environments: 
- Standard: language-specific runtimes
- Flexible: generalized container execution platform. Users create custom runtime env using Docker containers. Good for Applications with microservices. Always atleast 1 containr will be running (+ will be charged)


Supported Runtimes: Go, PHP, Java, Python, Node, .NET, Ruby

### Use Case 
When little OS/storage system config is needed 

## KUBERNETES ENGINE 

Features: 
- load balance across Compute Engine VMs
- Autoscaling for nodes 
- node monitoring and health repair 
- logging 
- node pools (collection of nodes with same config)
- automatic software version updates 

default Compute Engine VM is n1-standard-1

### Achieving HA with K8
- **eviction policy**: when resource starts consuming beyond set resource threshold, K8 starts shutting down pods 
- using deployments 
    - deployment states: 
        - 1) Progressing 
        - 2) Completed
        - 3) Failed 

### K8 Overhead
It scales: 
- 25% of first 4GB of memory, etc. 
- 6% of first CPU core, 1% of next, etc. upto 0.25%
