# Google Cloud Platform pentesting cheatsheet
## Index
* [General](#General)
* [Authenticated enumeration](authenticated-enumeration.md )
* [Privilege Escalation](privilege-escalation.md)
* [Persistence](persistence.md)
* [Post Exploitation](post-exploitation.md)

## General
### Metadata Service URL

```bash
curl "http://metadata.google.internal/computeMetadata/v1/?recursive=true&alt=text" -H "Metadata-Flavor: Google"
```
