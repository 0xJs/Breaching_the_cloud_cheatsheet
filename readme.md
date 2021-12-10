# Breaching the cloud cheatsheet
## Sumarry
* [General](#General)
* [Recon \ OSINT](recon.md)
* [Initial access attacks](initial-access-attacks.md)
* [Azure](Azure/readme.md)
* [Amazon Web Services](AWS/readme.md)
* [Google Cloud Platform](GCB/readme.md)

## General
- Google Cloud Platform != Google Workspace
- Azure != Microsoft 365
- Google Workspace and Mirosoft 365 are productivity suites
- Rules of engagement 
  - Azure https://www.microsoft.com/en-us/msrc/pentest-rules-of-engagement
  - AWS: https://aws.amazon.com/security/penetration-testing/
  - GCP  https://support.google.com/cloud/answer/6262505?hl=en
- Enumerate host https://github.com/dafthack/HostRecon

## Scanning tools
### Vulnerability scanning
- Scoutsuite
  - https://github.com/nccgroup/ScoutSuite
  - Scans AWS, Azure, GCP, Alibaba cloud, Oracle cloud
- Scoutsploit
  - https://github.com/cloudsploit/scans
  - Scans AWS, Azure, GCP, Oracle

### Enumeration
- WeirdAAL
  - https://github.com/carnal0wnage/weirdAAL
  - AWS

### Privesc scanning
- GCP IAM privilege esalation
  - https://github.com/RhinoSecurityLabs/GCP-IAM-Privilege-Escalation/tree/master/PrivEscScanner
  - GCP
- PACU
  - https://github.com/RhinoSecurityLabs/pacu
  - AWS 
- Stormspotter
  - https://github.com/Azure/Stormspotter
  - Azure
- Skyark
  - https://github.com/cyberark/SkyArk
