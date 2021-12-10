# Authenticated enumeration
-  Most GCP instances have Google Cloud SDK installed
-  ```gcloud``` CLI tool for managing auth, config, and interacting with GCP services
-  ``` gsutil``` CLI tool for accessing GCP storage buckets

## Manual enumeration
#### Get account information
```
gcloud config list
```

#### List organizations
```
gcloud organizations list
```

#### Enumerate IAM policies set ORG-wide
```
gcloud organizations get-iam-policy <ORG ID>
```

#### List projects
```
gcloud projects list
```

#### Set a different project
```
gcloud config set project <PROJECT NAME> 
```

#### Get access scopes if on an instance
```
curl http://metadata.google.internal/computeMetadata/v1/instance/serviceaccounts/default/scopes -H 'Metadata-Flavor:Google’
```

#### List other compute instances in the same project
```
gcloud compute instances list
```

#### List storage buckets
```
gsutil ls
```

#### List storage buckets recursively
```
gsutil ls -r gs://<bucket name>
```

#### List webapps
```
gcloud app instances list
```

#### List out SQL
```
gcloud sql instances list
gcloud sql databases list --instance <instance name>
gcloud spanner instances list
gcloud spanner databases list --instance <instance name>
gcloud bigtable instances list
```

### Networking
#### List networks
```
gcloud compute networks list
```

#### List subnets
```
gcloud compute networks subnets list
```

#### List VPN tunnels
```
gcloud compute vpn-tunnels list
```

#### List Interconnects (VPN)
```
gcloud compute interconnects list
```
