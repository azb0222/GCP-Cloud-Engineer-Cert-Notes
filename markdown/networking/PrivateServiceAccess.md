# PRVIATE SERVICE ACCESS

- **private services access**: private connection between YOUR VPC and GCP managed/third party managed VPC (ie. service producers)
  - allows for VM instances within own VPC to talk to services in service producers VPCs using internal IP addresses
  - 1) allocate CIDR block in own VPC to ensure no collision between own VPC + service producer VPC 2) create private connection to service producer 
  - private service access supported for: https://cloud.google.com/vpc/docs/private-services-access#private-services-supported-services


**steps**: 
1) allocate a CIDR block in your own VPC that  does not have any overlapping with any current or future subnets in your VPC. 
   1) must be atleast /24 block (or 256 addresses), reccomended for /16 block (65,536 addresses) for future scalability 
      1) remember that some services could require multiple IPs, etc. 
2) service provider creates subnet for resource, selects an IP address range from the larger block allocated from you
3) setup private connection (VPC Network Peering Connection) between your VPC and service provider 
4) as you create resources needing prviate IP addresses, service producer automatically handles subnet creation + IP address allocation; you do not have control over specific IPs

list allocated IP address ranges: `gcloud compute addresses list --global --filter="purpose=VPC_PEERING"`



# SERVERLESS VPC ACCESS CONNECTOR 
- 1) allocate CIDR block in own VPC to ensure no collision between own VPC + service producer VPC 
- 2) the Access connector creates connector instances (larger machines = higher throughput), min 2. scales instances as traffic increases, does not scale in. so set min,max = 2