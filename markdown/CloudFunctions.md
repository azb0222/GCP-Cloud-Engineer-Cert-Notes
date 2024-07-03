## Cloud Functions 
**Cloud Function**: run single purpose piece of code to respond to a GCP event, serverless. 
Support for Python, Node JS 6, Node JS 8 runtime environments 

### Key Features
1) A Cloud Function executes in a secure, isolated env 
2) Compute resources scale to run as many instances of Cloud Function as necessary 
3) Function execution lifecycles are independent 
    - Each invocation of a Cloud Function runs in a separate instance. Functions do not share memory or variables (functions are stateless)

**Trigger**: specific condition defining the source of events for Cloud Function that causes function to execute 
Triggers can be HTTP (POST, GET, PUT, DELETE, OPTIONS) or event driven 

**Event**: specific kinds of events within trigger source that can invoke a CloudFunction (ie. file upload in Cloud Storage) 
2nd gen: 
- HTTP (cert installed by default)
- Cloud Pub/Sub 
- Firestore

1st gen: 
- Cloud Storage 
- Cloud Pub/Sub 
- HTTP (cert installed by default)
- Firestore 
- Google Analytics 



### Comparsion 
- use App Engine for an application with several services 


### Specs 
- Cloud Functions will time out after 1 min <TODO: idk if this is accurage> 
- default memory is 256 MB (can set from 128 MiB to 32 GiB)

### Cloud Storage Integration 
Available Cloud Storage triggers: 
- `google.cloud.storage.object.v1.finalized`: when new object is created or existing object is overwritten 
- `google.cloud.storage.object.v1.deleted`
- `google.cloud.storage.object.v1.archived`
- `google.cloud.storage.object.v1.metadataUpdated`

### PubSub Integration 
- messages in PubSub are base64 encoded. Must decode within cloud function 


### gcloud commands: 

need to run `gcloud components update` for it to work (and `gcloud components install beta` if using Python)

`gcloud functions deploy cloud_storage_function_test \ --runtime <runtime> --trigger-resource <bucket name> --trigger-event <event trigger ie. google.storage.object.finalize>` 

`gcloud functions deploy myFunction --runtime nodejs --triger-http --allow-unauthenticated`

`gcloud functions delete <function-name>`

#TODO: create a sample Cloud Function Deployment via console + gcloud for PubSub and Cloud Storage 