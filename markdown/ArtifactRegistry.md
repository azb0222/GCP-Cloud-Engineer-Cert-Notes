#TODO: figure out Pricing, Vulnerability Scanning

# ARTIFACT REGISTRY 

Central place to store/manage artifacts (packages + Docker container images)

Heavy integration with other GCP resources (ie. store artifacts from Cloud Build, deploy artifacts to GKE, Cloud Run, App Engine Flexible, etc., IAM for credential management, GCP logging tools for security and logging) 

Artifacts can be grouped by team, dev stage, control access, etc.
Artifacts can be tagged, versioned, etc.  

## AR Repositories: 
storage locations where different types of artifacts (ie. container images, Java artifacts, Node.js packages, etc.) are stored

ex. Docker repository for container images, Maven repository for Java artifacts, etc. 

## Docker 

### Container Images
Image Path: 
"us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0"
us: location of repository 
docker.pkg.dev: hostname for container images 
google-samples: repository ID 
containers/gke: path to image under google-samples 