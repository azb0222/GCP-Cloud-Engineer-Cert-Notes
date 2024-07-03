## GENERAL 

### GCP Specialized Services: 

**AI** (all serverless + come with API): 
- AutoML: a machine learning service 
- Cloud Natural Language: a service for analyzing text 
- Cloud Vision: analyzing images 
- Cloud Inference API: service for computing correlations over time-series data

### Zones and Regions 

**Region**: geographic location. ie. us-east1

**Zone** zones within region are linked with low-latency, high-bandwidth network connections. atleast 3 zones per region. ie. us-east1-b, us-east1-c, us-east1-a


### Categorizing Resources 

**Tags**: used for network traffic management. apply firewall rules to instances based on specific tags 

**Labels**: key value pairs to organize, identify, filter resources more broadly. used for broader organizational purposes ie. billing, management, automation, resource grouping, etc. 

- There is no `Organization` resource given that we do not have a Google Workspace or Cloud Identity Account
- `Project` resources must be created manually (#TODO: include gcloud command here), as Terraform does not allow you to provision projects without an organization (https://stackoverflow.com/questions/60911807/terraform-google-cloud-why-cannot-i-create-a-new-project-without-having-an-org

### Limits and Quotas 

quota limits are updated once a day
will return 429/ResourceExhausted as error if quota is met  
Two types of quotas: 
1) Rate Quotas: resets after a specific time ie. number of API calls 
2) Allocation Quotas: must be explicity released ie. number of compute instances 

Can monitor quotas using Cloud Monitoring API and UI, can create custom alerts and dashboard on quota usage


