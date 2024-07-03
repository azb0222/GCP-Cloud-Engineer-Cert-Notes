These are my notes used while studying for the GCP Associate Cloud Engineer Certification. 

I followed the exam guide: https://cloud.google.com/learn/certification/guides/cloud-engineer. 

To apply what I learned, I setup various GCP resources via Terraform for UMass Cybersecurity Club. 


## Setup

I created a fresh GCP account for testing. You can access $300 free credit. 
``` 
iluvbbcand420@gmail.com
zuni497RANT
```

## Section 1: Setting up a cloud solution environment (~17.5%)

- **Prerequisites**: 
    - These identity accounts represent a company (+ associated domain): 
        - 1) Google Workspace: for businesses, create individual user accounts, etc. with domain 
        - 2) Cloud Identity: IDaaS (Identity as a Service), federate identities between Google + other identity providers (ie. Microsoft AD, etc.)

- **Resource Hierarchy**: Organization -> Folders -> Projects -> Individual GCP Resources (ie. Compute Engine, App Engine). 
    - a resource within the resource hierarchy inherits all IAM + organization policies applied on it's parent 
    - **Organization**: root node within hierarchy. An organization is linked with a Google Workspace or Cloud Identity account
        - all child resources (ie. Projects, Compute Engine, etc.) created under the Google Workspace/Cloud Identity domain will be children of the organization, not individual accounts that created them 
        - Google Workspace/Cloud Identity super admin: initially created indvidual account responsible for domain ownership verification + recovery contact. Must assign "Organization Administrator IAM role" to other users/groups for further day-to-day GCP management. 
        - Default IAM roles for all users with organization domain are: `roles/resourcemanager.projectCreator` and `roles/billing.creator` 
    - **Folder**: additional grouping/isolation between projects. can have nested folder structure 
        - Folders only available when organization resource exists. 
    - **Project**: 
        - note that for individual GCP accounts, a Project can have "No Organization Assigned" 
        - `projectId`: globally unique identifier across all , generated based on project name (but can edit). permenant
        - `projectNumber`: read only, automatically assigned number 
        - `owner` (#TODO include full role name here) granted to creator of project
        - #TODO: tags 
    - IAM policies can be set at organization, folder, and project level. policies are inherited 
        - IAM policy hierarchy follows same model as GCloud resource hierarchy 
    - #TODO: include gcloud commands 

    #TODO: include resource hierachy picture (with gcp workspace/cloud identity)


- **Organizational Policies**: #TODO 

- **IAM Roles**: 

## Section 2: Planning and configuring a cloud solution (~17.5%)

- **CloudDNS**: GCP global DNS service to 1) publish domain names to global DNS 2) public zones/records in DNS without having to manage own DNS servers + software 



    - Prerequisite (Notes about Domains): (#TODO: learn DNS commands from course)
        - DNS namespace: hierarchal structure to organize/manage domain names 
            - root level: . 
            - TLD (top level domain): directly below root, ex. .com, .org, .net, etc. 
            - SLD (second level domain): directly below TLD. includes name + TLD ex. example.com, test.org
            - subdomains: different sections of website ex. blog.example.com 
            - hostnames: specific names for specific devices/services ex. www.example.com, mail.example.com 
        - FQDN (Fully Qualified Domain Name): complete domain name with exact location in its DNS hierarchy, up to specific hostname ex. www.example.com. 
        - DNS zones: managed portion of DNS namespace. represents distinc part of domain tree + all of its containing domain names. defined in zone files (files with all mpapings for DNS servers)
        - DNS record: entry within DNS zone file to map domain name to IP address/other data: 
            - A: domain name -> IPv4 address 
            - AAAA: domain name -> IPv6 address 
            - CNAME: alias domain name -> canonical (true) domain name 
            - MX (Mail Exchange Record): mail server 
            - TXT: insert text into DNS record, used for domain/email verification 
            - NS: authoritative name servers for domain 
            - SOA (Start of Authority Record): admin info about zone 


            - things to figure out: 
                - how tf dns actually works in general 
                - how tf dns works in gcp 
                - how tf i use dns commands 
                - register my dns for personal site 
                - dns in kubernetes 
                - create my own dns thing?  




## Compute Engine 

### Compute Engine Overview 

TODO: still don't understand live migration without downtime + how to do? 

### General Terms 

- sole tenant node: dedicated physical server only for your VM. billing is based on total capacity of server, not VMs running on it 


#TODO: don't understand this fully 
- Shielded VM: verifiable integrity for Compute Engine VM to ensure no boot/kernel level root kits using Secure Boot, vtpm, measure boot, and integrity monitoring 
    - boot process for shielded VM starded with Secure Boot: digital siganature for all boot components is verified using Google CA, stops boot process if signature verification fails 
    1) Secure Boot 
    2) vTPM (Virtual Trusted Platform Module)/Measured Boot: specialized computer chip to protect confidential objects (ie. keys, certificates). Shielded VMs uses vTPM to do measured boot: performs measurements needed to create known integrity policy baseline (safe boot baseline) 
    3) Integrity Monitoring: used for comparsions with measurements from  Measured Boot to current boot measurements. returns pass or fail depending on match 


### Compute Engine Machine Types 

A compute engine instance can be categorized by: 
Machine Family: Broad classification based on different workload needs
  |_ Series: Generations or configurations within a family. newer series = better performance  
    |_ Machine Type: specific config of vCPUs, memory, other resources within a series 

#### Machine Families: 
1) General Purpose (available for custom machine types)
2) Compute Optimized: ultra high performance (ie. gaming, single threaded apps, high performance computing). highest performance per core. cannot use regional persistent disks, only availble in specfic zones 
3) Memory Optimized: ultra high memory workloads (ie. large in memory databases, in memory analytics). cannot use regional persistent disks, only availble in specfic zones 

#### Naming 
Machine Type Name: 
convention: series - type - vCPUs
ex:         e2 (2 = 2nd generation) - standard - 32

#### Series 
1) General Purpose: 
- e2: upto 32 vCPUs, upto 128 GB of memory. Low on-demand pricing 
- n1: upto 96 vCPUs, upto 624 GB of memory. balanced price/performance across wide range of VM shapes. only series w/ support for GPU/TPU (NVIDIA GPUs only). larger sustained use discount than N2 
- n2: upto 80 vCPUs, upto 640 GB of memory. workloads with high clock frequency, higher per-thread performance than n1
- n2d: upto 224 vCPUs, upto 896 GB of memory. 

2) Compute Optimized: 
- c2: upto 60 vCPUs, upto 240 GB of memory

3) Memory Optimized: 
- m1: upto 100 vCPU, upto 3844 GB of memory 
- m2: upto 100 vCPUs, upto 11776 GB of memory


#### Machine Types

1) General Purpose 
e2, n1, n2, n2d: 
- standard 
- highmem 
- highcpu 
- shared core: burstable workloads (for usage spikes), cost-effective, non-resource intensive application. share a physical core between vCPUs using context switching
    e2 (upto 2 vCPU, 4 GB of memory): 
    - e2-micro
    - e2-small
    - e2-medium 
    n1 (upto 2 vCPU, 4 GB of memory): 
    - f1-micro 
    - g1-small 

Can also create custom machine types (costs slightly more)

2) Compute Optimized
c2: 
- standard 

3) Memory Optimized 
m1, m2: 
- ultramem
- megamem


#### Instance Lifecycle 
1) Provisioning: resources (vCPU + memory, root disk + persistent disk, additional disks) are allocated 
2) Staging: instance is being prepared for first boot. internal/external IPs are allocated. system image is used to boot up instance 
3) Running: 
guest env (on Google public images) is automatically installed: set of scripts, daemons, binaries reading metadata server to make VM run 

metadata server: communication channel to transfer information from client to guest OS 

#TODO: i don't understand metadata 

- can do live migration with no VM downtime (for infra updates, fix failed hardware, etc.) from one zone -> one zone 

4) Suspending: preserves guest OS memory + application state (still pay for resources + external attached resources)
5) Stopping: temporary status before termination  (still pay for resources + external attached resources)
6) Terminating: (still pay for external attached resources)

### VM Metadata 

Metadata (key value pairs) can be assigned to both projects and instances. There is default metadata and custom metadata 

- VM stores metadata (key value pairs) on metadata server 
- VM has access to metadata server API w/ no additional authorization 
- Default VM metadata contains: info about VM, instance ID, hostname, zone, network, etc. 
- Custom VM metadata can contain: dynamic configuration values, startup/shut down scripts, service account tokens/creds (to avoid embedding creds for other GCP services within application code), instance tags/labels, etc. 


To query default instance metadata: 
`curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/`

To query default project metadata: 
`curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/project/`

To query custom metadata: use attributes directory (in both instance and project metadata)
- to add custom metadata for instance: `gcloud compute instances add-metadata <compute-instance-name> --metadata env=dev --zone <zone-name>`
- to add custom metadata for project: `gcloud compute project-info add-metadata --metadata KEY=VALUE`
- to query: `curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/attributes/`

#TODO: put more gcloud metadata commands here 

**Startup/Shutdown Scripts**: 
- stored in metadata: 
KEY: `startup-script`
VALUE: actual script 

or

- store in CloudStorage Bucket: 
upload shell script to bucket, copy URI, add `startup-script-url` (KEY) in metadata 

### Connecting to Instances 

Linux: 
- SSH (port 22)
- OS Login (uses Compute Enginge IAM roles to manage SSH access): links Linux user account to Google Identity (automatically generated SSH key is linked with user account) #TODO: is this automatically configured now? 
    - if member is not Owner role, need Compute OS Admin Login role or Compute OS Login role 
- project-level SSH keys (stored in Metadata): not secure ore recommended 

Windows: 
- RDP, RDP Client (port 3389)
- Powershell Terminal 
- Set windows password 


### Billing 

- resource based billing: each vCPU + GPU + GB of memory is billed separately despite using predefined machine types. charged by second (w minimum 60 seconds)
    - resource based pricing allows you to apply discounts (sustained use discount, commited use discount, preemtible VMs) to individual resources rather than machine types for more granular cost control 
- instance uptime: number of seconds between starting and terminating instance  

- Reservations: ensures that resources are availble when needed (resources can be consumed by any VM that needs it at that time, or a specific VM). will be billed even when not being used. Reservations only apply to Compute Engine, Dataproc, GKE VMs 

- sustained use discount (SUD): discount applied on VM used for more than 25% of a billing month, discount automatically applied for every incremental hour that the resource (vCPU, GPU, and memory) is used. discount increases with usage. applied automatically to VMs created by GKE and Compute Engine (not applied to App Engine)
    - Compute Engine breaks down vCPUs and memory used across all instances using predefined machine types, combines for largest sustained usage discounts possible 

- commited use discount (CUD): financial commitment to use a GCP service at a discounted rate. commit to specific resource usage (vCPU or memory) for 1-3 years, applies significant discount (57% discount for most resources, 70% for memory-optimized machine types). apply at project level, or can share across projects. 
commitments can be purcahsed on per region basis 
    - look in Reccomendations page 

- preemptible instance: cost-effective option (savings upto 80-90%) for workloads that can tolerate interruptions (upto 30 seconds warning beforehand). designed to be short-lived w max of 24 hours. use cases: batch processing, CI/CD, data anlysis, etc. workloads that can handle interruptions. fixed pricing. cannot live migrate or auto restart for maintenance  


### Storage (w. Compute Engine)

- root disk: primary boot disk attached to VM w. contains OS + various boot files + startup scripts/ config. 
- persistent disk: durable block storage device (HDD or SSD) for additional storage beyond root disk. contains user data, application files, databases, etc. can resize + type change without VM downtime.  independent of VM lifecycle. 

| Feature             | HDD Persistent Disks                      | SSD Persistent Disks                       | SSD Balanced Persistent Disks               |
|---------------------|-------------------------------------------|--------------------------------------------|---------------------------------------------|
| **Performance**     | Lower IOPS, higher latency                | High IOPS, low latency                     | Moderate IOPS, balanced latency             |
| **Cost**            | Most cost-effective for large volumes     | More expensive due to high performance     | Moderate cost, balancing performance and cost |
| **Best For**        | Sequential access, cold storage           | Random access, performance-critical tasks  | General-purpose workloads, medium-scale applications |
| **Typical Use Cases** | Logging, backups, data warehousing      | Databases, virtual machines, web servers   | Medium-scale databases, virtual machines, web servers |




definitions: 
mountable: can be attached at a mount point within the file system of an OS, allowing user/application to access those contents 
bootable: contains valid OS/bootloader, allowing computer to boot up from that device. when computer turns on, BIOs/ UEFI firmware will check for bootable device in boot order and load in OS from first bootable device 

Three types: 
1) Block Storage: 
fastest available, high performance storage type. data files are split into evenly split blocks each with UID. 
OS sees it as structureless raw data in form of a logical volume/hard drive. 
OS structures it with file system ext3/4 on Linux and ntfs on Windows 
delivered on physical media (spinning hard drives or Solid State Drives)
gcloud has: 
- persistent disk 
- local ssd 
as both mountable and bootable options 

can be used as the boot disks for compute engine 

2) File Storage: 
presented as traditional network file system similar to directory 
is mountable, but not bootable 

mountable, not bootable 

GCP service: Cloud FileStore. uses NFS protocol 

3) Object Storage: 
flat collection of unstructured data. holds no structure, made up of:
- data itself 
- metadata 
- global unique identifier 

GCP service: Cloud Storage: a flat storage organized by buckets 



#### Storage Performance Terms: 
- I/O: single read/write request. measured in block sizes (1 kb - 4 mb)
- I/O queue depth: number of pending I/O requests 
- IOPS (I/O operations/s): how many I/O operations can be performed per second 
- Throughput: speed at which data is transferred per second (MB/s)
- Latency: delay between time data is requested and when data is returned, measured in ms 

- Sequential Access: large single file access
- Random Access: loading application or OS (lots of little files). much slower than sequential access 

I left off on 12:05:10


