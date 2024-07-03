### Admin
#### Privacy 
Can disable Reccomendations until IAM & Admin -> Privacy and Security -> Transparency and Control 

### IAM (Identity and Access Management)

#### Overview 

IAM should follow "Principle of Least Privilege"

**Who (Identity) Has what access (Role) For which Resource** 

**IAM Policy**: collection of bindings, audit configuration, metadata. IAM policies are attached to resources at any level within the resource hierarchy (child resources inherit policies from parent resources)
    - bindings: bind one or members with a single role (and condition)
    - metadata: additional info (etags, version) about the policy 
    - audit config: config data of how access attempts should be audited 

**Principle**: identity that can access a resource 
  - Google Account: email associated w/ user
  - Service account: account for application instead of end user 
  - Google Group: named collection of Google Accounts/service accounts 
  - GSuite domain: virtual group of all user accounts in GSuite domain 
  - Cloud Identity domain: google accounts not tied to any GSuite application. virtual group of all user accounts in Cloud Identity domain 
  - AllAuthenticatedUsers: special identifier for all service accounts/users on internet with authenticated Google Account
  - AllUsers: special identifier for anyone on internet (authetnicated+unauthenticated)

**Roles**: named collection of permissions granting various accesses to GCP resources 
-  **Permissions**: determine what operations are allowed on a resource, correspond 1-1 with REST API methods
    naming: service.resource.verb ie. `compute.instances.list`
- Three types of roles: 
  - 1) Primitive: roles existing prior to introduction of IAM. Avoid roles if possible in favor of Predefined 
    - Owner
    - Editor 
    - Viewier 
  - 2) Predefined: finer grained access control than primitive roles. maintained by Google
  - 3) Custom: user defined. Only `Project Owners` can create new custom roles 

**Conditions**: define + enforce conditional, attribute-based access control for GCP resources. only grant resource access if certain conditions are true ie. grant temporary access for a certain time period 
- based on resource or details about request (ie. timestamp, IP address) 

**Metadata**: 
- etags: to prevent race conditions when updating the same IAM policy, use concurrency control using etag field. etag changes on each update 
- version: version number of IAM policy schema version
  - version 1: no support for conditional role bindings (default)
  - version 2: google internal use, will not use 
  - version 3: support for conditional role bindings 

**AuditConfig**: configure audit logging for policy. Determine which permission types are logged, what identites can be exempted from logging 
ex. 
```
auditConfigs: 
- auditLogConfigs: 
    - logType: DATA_READ
    - logType: ADMIN_WRITE
    service: allServices
- auditLogConfigs: 
    - exemptedMembers: 
        - asrithabodepudi@gmail.com
        logType: ADMIN_READ
    service: storage.googleapis.com
```

#### Example IAM Policy

JSON: 
```
{
    "bindings": [
        {
            "role": "roles/storage.admin", 
            "members": [
                "user:asrithabodepudi@gmail.com"
            ]
        }, 
        {
            "role": "roles/storage.objectViewer", 
            "members": [
                "user:test@gmail.com"
            ], 
            "condition": { 
                "title": "Expires Jan 1", 
                "description": "Expires Jan 1", 
                "expression": 
                "request.time < timestamp('2021-01-01T00:00:00:000Z')"
            }
        }
    ], 
    "etag": "Be#3lkdj2", 
    "version": 3
}
```

can also format in YAML

#### IAM Policy

Limitations: 
- 1 policy per resource 
- 100 conditional role bindings per policy 

Conditional Role Limitations: 
- limited to specific services 
- primitive roles are unsupported
- members cannot be of allUsers or allAuthenticatedUsers 
- 20 role bindings for same role and same member

#### GCloud CLI Commands 

- `gcloud projects get-iam-policy <project-name>` //get attached policies for project 
- `gcloud projects set-iam-policy <project-name> <policy-file>` //set policy for project 
- `gcloud projects add-iam-policy-binding <project-name> --member <member> --role <role>` //add a binding for a project policy  
- `gcloud resource-manager get-iam-policy <folder-id>` //get attached policies for folder 
- `gcloud organizations get-iam-policy <organization-id>` //get attached policies for organization


#### Service Accounts 
Service account: account for application instead of end user 

Three types: 
1) User-managed: user created, can create upto 100
naming format: `service-account-name@project-id.iam.gservice.account.com`
2) Default: automatically created (when you enable the API) and used by various services to perform tasks. comes with `Editor` role on Project by default. Reccomended to assign more granular permissions 
naming format: 
App Engine: `project-id@appspot.gserviceaccount.com`
Compute Engine: `project-number-compute@developer.gserviceaccount.com` 
3) Google-managed: created and managed by Google, used by Google services (some hidden, some visible). Reccommended to not edit permissions 


Service Account is identity AND resource -> so can have IAM policy attached to it to determine who can use the service account. 
- note that granting `Service Account User Role` to a user allows user to access all resources that said service account has access to 

##### Authentication 

each service account has two sets of key pairs 
  - 1) Google Managed Key Pairs: key generation, rotation, storage, etc. all handled by Google. IAM exposes APIs to use keys to sign on behalf of service account 
  - 2) User Managed Key Pairs: Google stores public key, use responsible for secure storage + rotation of private key. Reccomended to store in Cloud KMS 

##### Access Scopes 
Legacy Auth method (instead of IAM)  
used for default, automatically created service accounts 


##### Use Cases
1) Binding: Service Account to resource
ie. When you create a Compute Engine resource and keep default service account, it uses that service account (+ associated access scopes) to run all gcloud commands/access all gcloud services (run `gcloud config list` to check)
2) Direct Impersonation 

##### Best Practices 
- audit service accounts and keys w/ Logs Viewer page
- only assign minimum set of permissions 
- use IAM Serivce Account API for key rotation 
- audit IAM policy changes 
- can export audit logs to Cloud Storage 

#### Cloud Identity 
- IDaaS (Identity as a Service)
- User and Group Management
- Device Management (access work accounts from personal devices, can implement policies of approved applications, managed applications, etc.)
- SSO (Google is identity provider, dashboard with all available SSO applications)
- Reporting (Audit Logs for audits, groups, devices, etc. Export logs to BigQuery)
- Directory Management (Profile info for users within org, sync data with Microsoft AD/LDAP)
  - Google Cloud Directory Sync: sync AD users/groups (source of truth) with Cloud Identity. run on GCP or on prem 
- Identity Federation with AD, other identity providers (sign onto GCP using AD identity)

#### DRAFTING CYBERSEX IAM POLICY: 

- UMassCybersex is superadmin? 
- Create Google Group for DevOps 
- Create Goolge Group for Admin 


- add labels (in IAM) to all resources to break down billing by Label

todo to learn IAM: 
- test creating/adding policies for individual resources (but reccomended to create at project level) instead of just high level resource (ie. project)
- look at how to use access scope
- test service account usage w github actions or something 
- understand roles naming structure