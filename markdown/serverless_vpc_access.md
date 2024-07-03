# SERVERLESS VPC ACCESS

- allows for serverless resources (ie. Cloud Run, App Engine, Cloud Functions) to send requests to internal VPC network via DNS + internal IP addresses

**connector**: handles traffic between serverless env and VPC network 
- made of connector instances: based on machine types (minimum of 2, max of 10). does automatic scaling based on traffic. cannot scale in, so set max number of instances to low number to prevent too much costs 


every connector automatically has the 2 network tags (ie. instance tags). can used to modify firewall rules and routes
1) universal network tag:  `vpc-connector`
2) unique network tag: `vpc-connector-REGION-CONNECTOR_NAME`