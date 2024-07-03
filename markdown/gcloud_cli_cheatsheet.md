#GCLOUD CLI CHEATSHEET 

## config 
- `gcloud config list`
- `gcloud config configurations list` //manage multiple configurations 
- `gcloud config configurations activate <name>` //switch to a configuration 

- `gcloud config set`
- `gcloud config set compute/zone us-east1-b`
- `gcloud config set project <project-id>`

## projects 
- `gcloud projects list`
- `gcloud compute regions list --format="value(name)` //list by name only

## compute

### compute engine
- `gcloud compute images list` //see available images 

### zones and regions 
- `gcloud compute regions list` 
- `gcloud compute zones list`

### vpc
- `gcloud compute networks list`
- `gcloud compute networks update <network-name> --switch-to-custom-subnet-mode` //switch from auto mode to custom

### subnets 
- `gcloud compute networks subnets list`  
- `gcloud compute networks subnets list --filter="region:( us-central1 europe-west1 )"` //filter by region 


## cloud build 
- `gcloud builds submit --region=us-west2 --tag us-west2-docker.pkg.dev/project-id/quickstart-docker-repo/quickstart-image:tag1` //build and push to artifact registry