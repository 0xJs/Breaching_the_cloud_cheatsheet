# Lateral movement
## Steal access tokens
#### Google
-  Google JSON Tokens and credentials.db
-  JSON tokens typically used for service account access to GCP
-  If a user authenticates with gcloud from an instance their creds get stored here ```~/.config/gcloud/credentials.db```
```
sudo find /home -name "credentials.db"
```
